# qtui Status

`qtui` was extracted from Gazelle Installer as a standalone CMake library. The library currently provides the core terminal widgets needed by the installer TUI path and includes interactive tools for manual verification.

## Included Components

- Public headers under `include/qtui/`
- Implementations under `src/`
- Interactive test tools under `tests/`
- Example applications under `examples/`
- CMake install/export files for `find_package(qtui CONFIG)`

## Build Coverage

The standalone repository builds:

- `qtui::qtui`
- `test-tui`
- `debug-keys`
- `login-form`
- `installer-settings`
- `progress-demo`

The tools are interactive ncurses programs, so they are built as manual verification aids rather than registered as automated CTest cases.
