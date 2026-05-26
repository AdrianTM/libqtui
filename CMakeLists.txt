cmake_minimum_required(VERSION 3.21)

project(libqtui
    VERSION 0.1.0
    DESCRIPTION "Qt Core based terminal UI widgets"
    LANGUAGES CXX
)

option(QTUI_BUILD_TESTS "Build the interactive qtui test tools" ON)
option(QTUI_BUILD_EXAMPLES "Build qtui example applications" ON)
option(BUILD_SHARED_LIBS "Build shared libraries" ON)

add_subdirectory(qtui)
