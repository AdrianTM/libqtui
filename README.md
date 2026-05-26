# libqtui

`libqtui` contains `qtui`, a Qt 6 Core based terminal UI library that provides ncurses-rendered widgets with Qt-style signals and slots.

## Layout

- `qtui/include/qtui/` - public headers
- `qtui/src/` - library implementation
- `qtui/tests/` - interactive test tools
- `qtui/examples/` - example applications
- `qtui/README.md` - widget API overview
- `qtui/INTEGRATION.md` - integration notes for consumers
- `docs/` - extraction and migration notes

## Requirements

- CMake 3.21 or newer
- C++23 compiler
- Qt 6 Core
- ncursesw development headers and library

## Build

```bash
cmake --preset default
cmake --build --preset default
```

The default preset builds the shared library, interactive test tools, and example programs in `_build_/`.

To build just the library:

```bash
cmake -S . -B _build_/library-only -DQTUI_BUILD_TESTS=OFF -DQTUI_BUILD_EXAMPLES=OFF
cmake --build _build_/library-only
```

## Install

```bash
cmake --install _build_
```

The install step exports a CMake package named `qtui`, so consumers can use:

```cmake
find_package(qtui REQUIRED CONFIG)
target_link_libraries(my_app PRIVATE qtui::qtui)
```

Projects can also vendor this repository and use:

```cmake
add_subdirectory(libqtui/qtui)
target_link_libraries(my_app PRIVATE qtui::qtui)
```

## License

GNU General Public License version 3. See `LICENSE` and `NOTICE`.
