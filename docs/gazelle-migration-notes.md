# Gazelle Installer Migration Notes

The original `qtui` work was developed inside Gazelle Installer. The useful extraction pattern is to keep installer business logic separate from the UI layer:

- shared core code owns installation decisions and progress reporting
- GUI code maps Qt Widgets to the shared core
- TUI code maps `qtui::T*` widgets to the same shared core
- mode selection can live in the application entry point, outside `libqtui`

This repository intentionally keeps only the reusable `qtui` library and standalone examples. Gazelle-specific wrappers and launch decisions should stay in Gazelle Installer or another consuming application.
