# qtui Integration Guide

This guide covers the two supported ways to consume `qtui`: as vendored source or as an installed CMake package.

## Vendored Source

Add the `qtui` directory to your project and link the namespaced target:

```cmake
add_subdirectory(qtui)
target_link_libraries(my_app PRIVATE qtui::qtui)
```

If you do not want the interactive tools and examples in a vendored build:

```cmake
set(QTUI_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(QTUI_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
add_subdirectory(qtui)
```

## Installed Package

Install from the `libqtui` repository root:

```bash
cmake --preset default
cmake --build --preset default
cmake --install build --config Debug --prefix /usr/local
```

Then consume it from another CMake project:

```cmake
find_package(qtui REQUIRED CONFIG)
target_link_libraries(my_app PRIVATE qtui::qtui)
```

## Minimal Application

```cpp
#include <QCoreApplication>
#include <qtui/application.h>
#include <qtui/tmessagebox.h>

int main(int argc, char *argv[])
{
    QCoreApplication coreApp(argc, argv);
    qtui::Application tuiApp(argc, argv);
    tuiApp.enableMouse();

    qtui::TMessageBox::information(nullptr, "Hello", "Welcome to qtui");
    return 0;
}
```

## Event Loop Pattern

Most widgets are rendered manually. A simple button loop looks like this:

```cpp
#include <QCoreApplication>
#include <qtui/application.h>
#include <qtui/tpushbutton.h>
#include <ncurses.h>

int main(int argc, char *argv[])
{
    QCoreApplication coreApp(argc, argv);
    qtui::Application tuiApp(argc, argv);
    tuiApp.enableMouse();

    qtui::TPushButton okButton("OK");
    okButton.setPosition(10, 20);
    okButton.setFocus(true);
    okButton.show();

    bool running = true;
    QObject::connect(&okButton, &qtui::TPushButton::clicked, [&]() {
        running = false;
    });

    while (running) {
        clear();
        okButton.render();
        refresh();

        const int ch = getch();
        if (ch == 27) {
            running = false;
        } else {
            okButton.handleKey(ch);
        }
    }

    return 0;
}
```

## Focus and Input Dispatch

Focus is caller-managed. Call `setFocus(true)` on the active widget and `setFocus(false)` on others when Tab is pressed. Route the key from `getch()` to the focused widget:

```cpp
if (focusIndex == 0) fieldA.handleKey(ch);
else if (focusIndex == 1) fieldB.handleKey(ch);
```

For mouse input, broadcast to all widgets and check the return value:

```cpp
if (ch == KEY_MOUSE) {
    MEVENT ev;
    if (getmouse(&ev) == OK && (ev.bstate & BUTTON1_CLICKED)) {
        if (myButton.handleMouse(ev.y, ev.x))
            myButton.click();
    }
}
```

## Special Render Calls

Two widgets require additional calls outside the normal render pass:

- **`TLineEdit::showCursor()`** — call after `refresh()` for the focused `TLineEdit` to position the ncurses cursor correctly.
- **`TComboBox::renderPopup()`** — call after all other `render()` calls when `isPopupVisible()` is true, so the dropdown draws on top.

## Animation / Non-blocking Loop

Use `::timeout(ms)` to make `getch()` non-blocking with a millisecond delay. Reset to `::timeout(-1)` when done:

```cpp
::timeout(50);  // getch() returns ERR after 50 ms if no input
while (running) {
    // update state, render, getch() ...
}
::timeout(-1);  // back to blocking
```

## Widget Mapping

Qt GUI widget equivalents use the `T` prefix in this library:

- `QMessageBox` -> `qtui::TMessageBox`
- `QCheckBox` -> `qtui::TCheckBox`
- `QRadioButton` -> `qtui::TRadioButton` (group via `qtui::TButtonGroup`)
- `QPushButton` -> `qtui::TPushButton`
- `QLabel` -> `qtui::TLabel`
- `QLineEdit` -> `qtui::TLineEdit`
- `QComboBox` -> `qtui::TComboBox`
- `QProgressBar` -> `qtui::TProgressBar`
- `QSlider` -> `qtui::TSlider`
- `QGroupBox` -> `qtui::TGroupBox`
- `QDialog` -> `qtui::TDialog` (subclass and override `renderContent()`)

## Dependencies

Consumers need Qt 6 Core and ncursesw development files. The exported `qtui::qtui` target carries the include and link requirements for both dependencies.
