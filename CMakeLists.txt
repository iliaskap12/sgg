cmake_minimum_required(VERSION 3.15.7)

# FetchContent added in CMake 3.11, downloads during the configure step
include(FetchContent)
# FetchContent_MakeAvailable was not added until CMake 3.14; use our shim
if (${CMAKE_VERSION} VERSION_LESS 3.14)
    include(cmake/add_FetchContent_MakeAvailable.cmake)
endif ()

FetchContent_Declare(
  glew
  GIT_REPOSITORY https://github.com/Perlmint/glew-cmake.git
  GIT_TAG        origin/master
)
FetchContent_GetProperties(glew)
if(NOT glew_POPULATED)
  FetchContent_Populate(glew)

  add_subdirectory(${glew_SOURCE_DIR} ${glew_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

FetchContent_Declare(
        glm
        GIT_REPOSITORY https://github.com/g-truc/glm.git)
FetchContent_MakeAvailable(glm)
FetchContent_GetProperties(glm)
if(NOT glm_POPULATED)
  FetchContent_Populate(glm)

  add_subdirectory(${glm_SOURCE_DIR} ${glm_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

FetchContent_Declare(
        SDL
        GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
        GIT_TAG main)
FetchContent_MakeAvailable(SDL)
if(NOT SDL_POPULATED)
  FetchContent_Populate(SDL)

  add_subdirectory(${SDL_SOURCE_DIR} ${SDL_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

FetchContent_Declare(
        freetype
        GIT_REPOSITORY https://github.com/freetype/freetype.git)
FetchContent_MakeAvailable(freetype)
if(NOT freetype_POPULATED)
  FetchContent_Populate(freetype)

  add_subdirectory(${freetype_SOURCE_DIR} ${freetype_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

project(sgg VERSION 1.0.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 23)

list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

find_package (OpenGL REQUIRED)
#find_package(Wrap_GLEW MODULE REQUIRED)
#find_package(Wrap_glm MODULE REQUIRED)
#find_package(Wrap_SDL2 MODULE REQUIRED)
find_package(Wrap_Freetype MODULE REQUIRED) 
#find_package(Wrap_SDL2_mixer MODULE REQUIRED)

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
)

target_link_libraries(sgg
    PUBLIC
    glm
    libglew_static
    SDL
    freetype
    $<TARGET_NAME_IF_EXISTS:SDL2::SDL2_mixer>
)

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
