cmake_minimum_required(VERSION 3.18)
project(RemeshPortal VERSION 0.6.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()

option(RP_BUILD_TESTS "Build the Qt-free core self-test (core_probe)" ON)

find_package(Qt5 5.15 REQUIRED COMPONENTS Core Gui Widgets Concurrent)
find_package(OpenGL REQUIRED)

# ------------------------------------------------------------------ core (Qt-free)
set(VCG_DIR ${CMAKE_CURRENT_SOURCE_DIR}/third_party/vcglib)
if(NOT EXISTS ${VCG_DIR}/vcg/complex/complex.h)
    message(FATAL_ERROR "VCGLib not found in third_party/vcglib")
endif()

add_library(rp_core STATIC
    src/core/MeshOps.cpp
    src/core/MeshOps.h
    src/core/Reduce.cpp
    src/core/Reduce.h
    src/core/Subdivide.cpp
    src/core/Subdivide.h
    src/core/Remesh.cpp
    src/core/Remesh.h
    src/core/QuadConvert.cpp
    src/core/QuadConvert.h
    src/core/FreeFlow.cpp
    src/core/FreeFlow.h
    src/core/MeshTypes.h
    src/core/MeshStats.h
    src/core/MeshDocument.h
    src/core/RenderMesh.h)
target_include_directories(rp_core PUBLIC src ${VCG_DIR} ${VCG_DIR}/eigenlib)
target_compile_definitions(rp_core PUBLIC NOMINMAX _USE_MATH_DEFINES _CRT_SECURE_NO_WARNINGS)
if(MSVC)
    target_compile_options(rp_core PUBLIC /utf-8 /bigobj /wd4244 /wd4267 /wd4305 /wd4996 /wd4018)
endif()

# ------------------------------------------------------------------ application
if(WIN32)
    enable_language(RC)
    set(RP_WIN_RC resources/app.rc)
endif()

add_executable(RemeshPortal WIN32
    src/main.cpp
    src/app/DarkTheme.cpp   src/app/DarkTheme.h
    src/app/Viewport.cpp    src/app/Viewport.h
    src/app/InfoPanel.cpp   src/app/InfoPanel.h
    src/app/DisplayPanel.cpp src/app/DisplayPanel.h
    src/app/ReducePanel.cpp src/app/ReducePanel.h
    src/app/SubdividePanel.cpp src/app/SubdividePanel.h
    src/app/ResamplePanel.cpp src/app/ResamplePanel.h
    src/app/QuadPanel.cpp src/app/QuadPanel.h
    src/app/FreeFlowPanel.cpp src/app/FreeFlowPanel.h
    src/app/QuadWildRunner.cpp src/app/QuadWildRunner.h
    src/app/BusyDialog.cpp  src/app/BusyDialog.h
    src/app/DockTitleBar.h
    src/app/ToolsPanel.h
    src/app/MainWindow.cpp  src/app/MainWindow.h
    resources/resources.qrc
    ${RP_WIN_RC})

target_link_libraries(RemeshPortal PRIVATE rp_core
    Qt5::Core Qt5::Gui Qt5::Widgets Qt5::Concurrent OpenGL::GL)
target_compile_definitions(RemeshPortal PRIVATE RP_VERSION="${PROJECT_VERSION}")
if(MSVC)
    # GUI subsystem, but keep a plain main()
    target_link_options(RemeshPortal PRIVATE /ENTRY:mainCRTStartup)
endif()

# ------------------------------------------------------------------ tests
if(RP_BUILD_TESTS)
    add_executable(core_probe tests/core_probe.cpp)
    target_link_libraries(core_probe PRIVATE rp_core)

    # integration test of the QuadWild runner (needs the real tools, see tests/runner_probe.cpp)
    add_executable(runner_probe tests/runner_probe.cpp src/app/QuadWildRunner.cpp src/app/QuadWildRunner.h)
    target_include_directories(runner_probe PRIVATE src)
    target_link_libraries(runner_probe PRIVATE rp_core Qt5::Core)
endif()
