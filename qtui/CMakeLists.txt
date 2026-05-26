cmake_minimum_required(VERSION 3.21)

if(CMAKE_SOURCE_DIR STREQUAL CMAKE_CURRENT_SOURCE_DIR)
    project(qtui VERSION 0.1.0 LANGUAGES CXX)
endif()

include(GNUInstallDirs)
include(CMakePackageConfigHelpers)

if(NOT DEFINED QTUI_BUILD_TESTS)
    set(QTUI_BUILD_TESTS ON)
endif()

if(NOT DEFINED QTUI_BUILD_EXAMPLES)
    set(QTUI_BUILD_EXAMPLES OFF)
endif()

set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_AUTOMOC ON)

find_package(Qt6 REQUIRED COMPONENTS Core)
set(CURSES_NEED_NCURSES TRUE)
set(CURSES_NEED_WIDE TRUE)
find_package(Curses REQUIRED)

add_library(qtui
    include/qtui/widget.h
    include/qtui/application.h
    include/qtui/tmessagebox.h
    include/qtui/tcheckbox.h
    include/qtui/tradiobutton.h
    include/qtui/tpushbutton.h
    include/qtui/tlabel.h
    include/qtui/tlineedit.h
    include/qtui/tcombobox.h
    include/qtui/tprogressbar.h
    include/qtui/tslider.h
    include/qtui/tgroupbox.h
    include/qtui/tdialog.h
    src/widget.cpp
    src/tmessagebox.cpp
    src/application.cpp
    src/tcheckbox.cpp
    src/tradiobutton.cpp
    src/tpushbutton.cpp
    src/tlabel.cpp
    src/tlineedit.cpp
    src/tcombobox.cpp
    src/tprogressbar.cpp
    src/tslider.cpp
    src/tgroupbox.cpp
    src/tdialog.cpp
)
add_library(qtui::qtui ALIAS qtui)

target_include_directories(qtui
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
        ${CURSES_INCLUDE_DIRS}
)

target_link_libraries(qtui
    PUBLIC
        Qt6::Core
        ${CURSES_LIBRARIES}
)

target_compile_options(qtui PRIVATE
    -Wall -Wextra -Wpedantic
)

if(QTUI_BUILD_TESTS)
    add_subdirectory(tests)
endif()

if(QTUI_BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()

set(qtui_install_cmakedir "${CMAKE_INSTALL_LIBDIR}/cmake/qtui")

configure_package_config_file(
    cmake/qtuiConfig.cmake.in
    "${CMAKE_CURRENT_BINARY_DIR}/qtuiConfig.cmake"
    INSTALL_DESTINATION "${qtui_install_cmakedir}"
)

write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/qtuiConfigVersion.cmake"
    VERSION 0.1.0
    COMPATIBILITY SameMajorVersion
)

install(TARGETS qtui
    EXPORT qtuiTargets
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
    INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(DIRECTORY include/qtui
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
)

install(EXPORT qtuiTargets
    FILE qtuiTargets.cmake
    NAMESPACE qtui::
    DESTINATION "${qtui_install_cmakedir}"
)

install(FILES
    "${CMAKE_CURRENT_BINARY_DIR}/qtuiConfig.cmake"
    "${CMAKE_CURRENT_BINARY_DIR}/qtuiConfigVersion.cmake"
    DESTINATION "${qtui_install_cmakedir}"
)
