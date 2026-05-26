add_executable(test-tui
    test-tui.cpp
)

target_link_libraries(test-tui
    PRIVATE
        qtui::qtui
)

add_executable(debug-keys
    debug-keys.cpp
)

target_link_libraries(debug-keys
    PRIVATE
        qtui::qtui
)
