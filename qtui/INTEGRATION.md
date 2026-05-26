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

## Widget Mapping

Qt GUI widget equivalents use the `T` prefix in this library:

- `QMessageBox` -> `qtui::TMessageBox`
- `QCheckBox` -> `qtui::TCheckBox`
- `QRadioButton` -> `qtui::TRadioButton`
- `QPushButton` -> `qtui::TPushButton`
- `QLabel` -> `qtui::TLabel`
- `QLineEdit` -> `qtui::TLineEdit`
- `QComboBox` -> `qtui::TComboBox`
- `QProgressBar` -> `qtui::TProgressBar`
- `QSlider` -> `qtui::TSlider`
- `QGroupBox` -> `qtui::TGroupBox`
- `QDialog` -> `qtui::TDialog`

## Dependencies

Consumers need Qt 6 Core and ncursesw development files. The exported `qtui::qtui` target carries the include and link requirements for both dependencies.
