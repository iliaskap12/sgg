cmake_minimum_required(VERSION 3.15.7)

# FetchContent added in CMake 3.11, downloads during the configure step
include(FetchContent)
# FetchContent_MakeAvailable was not added until CMake 3.14; use our shim
if (${CMAKE_VERSION} VERSION_LESS 3.14)
    include(cmake/add_FetchContent_MakeAvailable.cmake)
endif ()

add_subdirectory(deps)

project(sgg VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)

find_package (OpenGL REQUIRED)

add_library(sgg
    sgg/audio.cpp
    sgg/AudioManager.cpp
    sgg/fonts.cpp
    sgg/GLbackend.cpp
    sgg/graphics.cpp
    sgg/lodepng.cpp
    sgg/shader.cpp
    sgg/texture.cpp
)

target_include_directories(sgg
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include>
    ${SDL2_INCLUDE_DIR}
)

target_link_libraries(sgg PRIVATE freetype SDL2 SDL2_mixer glm GLEW)

include(cmake/Installation.cmake)

add_library(sgg::sgg ALIAS sgg)

add_executable(demo
    demo/demo.cpp
)

target_include_directories(demo
    PRIVATE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/demo>
)

target_link_libraries(demo
    PRIVATE
    sgg::sgg
)
