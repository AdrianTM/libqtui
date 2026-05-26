# qtui

`qtui` is a small terminal widget library built on Qt 6 Core and ncurses. It keeps Qt-style `QObject` signals and slots while rendering widgets in a terminal.

## Widgets

- `qtui::TMessageBox`
- `qtui::TCheckBox`
- `qtui::TRadioButton` and `qtui::TButtonGroup`
- `qtui::TPushButton`
- `qtui::TLabel`
- `qtui::TLineEdit`
- `qtui::TComboBox`
- `qtui::TProgressBar`
- `qtui::TSlider`
- `qtui::TGroupBox`
- `qtui::TDialog`

## Basic Example

```cpp
#include <QCoreApplication>
#include <qtui/application.h>
#include <qtui/tmessagebox.h>

int main(int argc, char *argv[])
{
    QCoreApplication coreApp(argc, argv);
    qtui::Application tuiApp(argc, argv);
    tuiApp.enableMouse();

    qtui::TMessageBox::information(nullptr, "qtui", "Hello from the terminal");
    return 0;
}
```

## Build

When this directory is used directly:

```bash
cmake -S . -B build
cmake --build build
```

When this repository is used from the `libqtui` root:

```bash
cmake --preset default
cmake --build --preset default
```

## CMake Integration

Vendored source:

```cmake
add_subdirectory(qtui)
target_link_libraries(my_app PRIVATE qtui::qtui)
```

Installed package:

```cmake
find_package(qtui REQUIRED CONFIG)
target_link_libraries(my_app PRIVATE qtui::qtui)
```

## Notes

`qtui::Application` owns ncurses initialization and cleanup. Create one before rendering widgets that depend on the terminal state. `TMessageBox::exec()` can initialize ncurses for simple standalone message boxes, but applications should still prefer an explicit `qtui::Application`.
