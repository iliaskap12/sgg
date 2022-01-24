# FetchContent added in CMake 3.11, downloads during the configure step
include(FetchContent)
# FetchContent_MakeAvailable was not added until CMake 3.14; use our shim
if (${CMAKE_VERSION} VERSION_LESS 3.14)
    include(cmake/add_FetchContent_MakeAvailable.cmake)
endif ()

FetchContent_Declare(
        glm
        GIT_REPOSITORY https://github.com/g-truc/glm.git
        GIT_TAG 0.9.9.8
)
FetchContent_MakeAvailable(glm)
FetchContent_GetProperties(glm)
if (NOT glm_POPULATED)
    FetchContent_Populate(glm)
    add_subdirectory(${glm_SOURCE_DIR} ${glm_BINARY_DIR})
endif ()

FetchContent_Declare(
        glew
        GIT_REPOSITORY https://github.com/Perlmint/glew-cmake.git
)
FetchContent_MakeAvailable(glew)
FetchContent_GetProperties(glew)
if (NOT glew_POPULATED)
    FetchContent_Populate(glew)
    add_subdirectory(${glew_SOURCE_DIR} ${glew_BINARY_DIR})
endif ()
if(NOT TARGET GLEW)
    add_library(GLEW ALIAS libglew_shared)
endif()

FetchContent_Declare(
        freetype
        GIT_REPOSITORY https://github.com/freetype/freetype.git
)

FetchContent_GetProperties(freetype)
if (NOT freetype_POPULATED)
    FetchContent_Populate(freetype)
    add_subdirectory(${freetype_SOURCE_DIR} ${freetype_BINARY_DIR})
endif ()

FetchContent_Declare(
        SDL2
        GIT_REPOSITORY https://github.com/libsdl-org/SDL.git
        GIT_TAG main
)
FetchContent_MakeAvailable(SDL2)
FetchContent_GetProperties(SDL2)
file(GLOB INCLUDE_FILES ${SDL2_SOURCE_DIR}/include/*.h)
file(GLOB BIN_INCLUDE_FILES ${SDL2_BINARY_DIR}/include/*.h)
foreach(_FNAME ${BIN_INCLUDE_FILES})
    get_filename_component(_INCNAME ${_FNAME} NAME)
    list(REMOVE_ITEM INCLUDE_FILES ${SDL2_SOURCE_DIR}/include/${_INCNAME})
endforeach()
list(APPEND INCLUDE_FILES ${BIN_INCLUDE_FILES})

if(NOT EXISTS "${SDL2_BINARY_DIR}/include/SDL2")
    file(MAKE_DIRECTORY "${SDL2_BINARY_DIR}/include/SDL2")
    file(COPY ${INCLUDE_FILES} DESTINATION "${SDL2_BINARY_DIR}/include/SDL2")
endif()

set(SDL2_INCLUDE_DIR "${SDL2_BINARY_DIR}/include" PARENT_SCOPE)

FetchContent_Declare(
        SDL2_mixer
        GIT_REPOSITORY https://github.com/libsdl-org/SDL_mixer.git
)
FetchContent_MakeAvailable(SDL2_mixer)
FetchContent_GetProperties(SDL2_mixer)

file(GLOB SDL2_mixer_INCLUDE_FILES ${SDL2_mixer_SOURCE_DIR}/include/*.h)
file(COPY ${SDL2_mixer_INCLUDE_FILES} DESTINATION "${SDL2_BINARY_DIR}/include/SDL2")
#if (NOT SDL2_mixer_POPULATED)
#    FetchContent_Populate(SDL2_mixer)
#    add_subdirectory(${SDL2_mixer_SOURCE_DIR} ${SDL2_mixer_BINARY_DIR})
#endif ()
