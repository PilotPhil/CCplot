﻿#Qt5.15.2
#QCustomPlot2.1.0

cmake_minimum_required(VERSION 3.5)

project(CCplot VERSION 0.1 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

SET(LIBRARY_OUTPUT_PATH ${CMAKE_CURRENT_LIST_DIR}/install/lib)

if(CMAKE_MSVC_PARALLEL)
  if(CMAKE_MSVC_PARALLEL GREATER 0)
    add_definitions(/MP${CMake_MSVC_PARALLEL})
  else()
    add_definitions(/MP)
  endif()
endif()

find_package(QT NAMES Qt6 Qt5 COMPONENTS Widgets REQUIRED)
find_package(Qt${QT_VERSION_MAJOR} COMPONENTS Widgets PrintSupport REQUIRED)

include_directories(
        ${CMAKE_CURRENT_LIST_DIR}/source/base
        ${CMAKE_CURRENT_LIST_DIR}/source/dialog
        ${CMAKE_CURRENT_LIST_DIR}/source/qcustomplot
        ${CMAKE_CURRENT_LIST_DIR}/source/prop)

file(GLOB_RECURSE PROJECT_SOURCES
    ${CMAKE_CURRENT_LIST_DIR}/source/*.*)


file(GLOB_RECURSE HEADER_FILES
    ${CMAKE_CURRENT_LIST_DIR}/source/*.h)

file(COPY ${HEADER_FILES} DESTINATION ${CMAKE_CURRENT_LIST_DIR}/install/include)

file(COPY D:/Code/CCplot/source/ccplot.qrc DESTINATION ${CMAKE_CURRENT_LIST_DIR}/install/resource)

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(CCplot
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
# Define target properties for Android with Qt 6 as:
#    set_property(TARGET CCplot APPEND PROPERTY QT_ANDROID_PACKAGE_SOURCE_DIR
#                 ${CMAKE_CURRENT_SOURCE_DIR}/android)
# For more information, see https://doc.qt.io/qt-6/qt-add-executable.html#target-creation
else()
    if(ANDROID)
        add_library(CCplot SHARED
            ${PROJECT_SOURCES}
        )
# Define properties for Android with Qt 5 after find_package() calls as:
#    set(ANDROID_PACKAGE_SOURCE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/android")
    else()
        add_executable(ccplot
            ${PROJECT_SOURCES}
        )


    endif()
endif()

target_link_libraries(ccplot PRIVATE Qt${QT_VERSION_MAJOR}::Widgets
                                     Qt${QT_VERSION_MAJOR}::PrintSupport)


if(CMAKE_BUILD_TYPE AND (CMAKE_BUILD_TYPE STREQUAL "Debug"))
    add_library(LibCCPlotD STATIC ${PROJECT_SOURCES})
    target_link_libraries(LibCCPlotD PRIVATE Qt${QT_VERSION_MAJOR}::Widgets
                                     Qt${QT_VERSION_MAJOR}::PrintSupport)
elseif(CMAKE_BUILD_TYPE AND (CMAKE_BUILD_TYPE STREQUAL "Release"))
    add_library(LibCCPlot STATIC ${PROJECT_SOURCES})
    target_link_libraries(LibCCPlot PRIVATE Qt${QT_VERSION_MAJOR}::Widgets
                                     Qt${QT_VERSION_MAJOR}::PrintSupport)
else()
endif()

set_target_properties(ccplot PROPERTIES
    MACOSX_BUNDLE_GUI_IDENTIFIER my.example.com
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(CCplot)
endif()
