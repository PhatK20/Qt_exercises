# Specify the minimum version of CMake required
cmake_minimum_required(VERSION 3.21)

# Set up the project with its name, version, and language (C++)
project(Notepad VERSION 0.1 LANGUAGES CXX)

# Enable automatic handling of UI files (User Interface Compiler), MOC (Meta-Object Compiler),
# and RCC (Resource Compiler) during build
set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

# Set the C++ standard to 17 and make it required
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Find the Qt libraries with specified components (Widgets and PrintSupport)
find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets PrintSupport)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets PrintSupport)

# Define the source files for the project
set(PROJECT_SOURCES
        main.cpp
        mainwindow.cpp
        mainwindow.h
        mainwindow.ui
)

# Configure the project based on the Qt version (Qt6 or Qt5)
if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(Notepad
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
        Resources.qrc

    )
else()
    if(ANDROID)
        add_library(Notepad SHARED
            ${PROJECT_SOURCES}
        )
    else()
        add_executable(Notepad
            ${PROJECT_SOURCES}
        )
    endif()
endif()

# Link the Notepad target with the Qt Widgets and PrintSupport libraries
target_link_libraries(Notepad PRIVATE Qt${QT_VERSION_MAJOR}::Widgets Qt${QT_VERSION_MAJOR}::PrintSupport)

# Set properties for the target, such as bundle identifier and version for macOS
if(${QT_VERSION} VERSION_LESS 6.1.0)
  set(BUNDLE_ID_OPTION MACOSX_BUNDLE_GUI_IDENTIFIER com.example.Notepad)
endif()
set_target_properties(Notepad PROPERTIES
    ${BUNDLE_ID_OPTION}
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

# Specify installation directories for the Notepad target
include(GNUInstallDirs)
install(TARGETS Notepad
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

# Finalize the executable for Qt 6
if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(Notepad)
endif()
