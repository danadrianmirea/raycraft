cmake_minimum_required(VERSION 3.16)

project(raycraft LANGUAGES C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_C_STANDARD_REQUIRED ON)
set(CMAKE_C_EXTENSIONS OFF)

if(MSVC)
    message(WARNING "This project is written for GCC/MinGW-style C. Prefer configuring with MinGW (for example: -G \"MinGW Makefiles\").")
endif()

# Local raylib build directory containing raylib.h and raylib library files.
set(RAYLIB_DIR "C:/raylib/raylib/src" CACHE PATH "Path to raylib src directory")

find_path(RAYLIB_INCLUDE_DIR
    NAMES raylib.h
    PATHS "${RAYLIB_DIR}"
    NO_DEFAULT_PATH
)

find_library(RAYLIB_LIBRARY
    NAMES raylib libraylib
    PATHS "${RAYLIB_DIR}"
    NO_DEFAULT_PATH
)

if(NOT RAYLIB_INCLUDE_DIR)
    message(FATAL_ERROR "Could not find raylib.h in RAYLIB_DIR='${RAYLIB_DIR}'")
endif()

if(NOT RAYLIB_LIBRARY)
    message(FATAL_ERROR "Could not find raylib library in RAYLIB_DIR='${RAYLIB_DIR}'")
endif()

add_executable(raycraft
    src/main.c
    src/game_camera.c
    src/game_mouse.c
    src/player.c
    src/world/perlin.c
    src/world/worldgeneration.c
    src/world/worldmodel.c
)

target_include_directories(raycraft PRIVATE
    src
    "${RAYLIB_INCLUDE_DIR}"
)

target_link_libraries(raycraft PRIVATE "${RAYLIB_LIBRARY}")

if(WIN32)
    target_link_libraries(raycraft PRIVATE opengl32 gdi32 winmm)
elseif(APPLE)
    target_link_libraries(raycraft PRIVATE
        "-framework OpenGL"
        "-framework Cocoa"
        "-framework IOKit"
        "-framework CoreVideo"
    )
elseif(UNIX)
    target_link_libraries(raycraft PRIVATE m pthread dl rt GL X11)
endif()
