cmake_minimum_required(VERSION 3.21)
project(qtui-examples VERSION 0.1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_AUTOMOC ON)

if(NOT TARGET qtui::qtui)
    find_package(qtui REQUIRED CONFIG)
endif()

# Login form example
add_executable(login-form login-form.cpp)
target_link_libraries(login-form
    qtui::qtui
)

# Installer settings example
add_executable(installer-settings installer-settings.cpp)
target_link_libraries(installer-settings
    qtui::qtui
)

# Progress demo example
add_executable(progress-demo progress-demo.cpp)
target_link_libraries(progress-demo
    qtui::qtui
)
