cmake_minimum_required(VERSION 3.6)
project(vkquake)

# TODO WITH_CODEC_MP3 WITH_CODEC_VORBIS

set(PLATFORM "x11" CACHE STRING "Windowing platform to use with Vulkan (x11/wayland)")

option(WITH_CODEC_FLAC "USE_CODEC_FLAC" OFF)
option(WITH_CODEC_MIKMOD "USE_CODEC_MIKMOD" OFF)
option(WITH_CODEC_MODPLUG "USE_CODEC_MODPLUG" OFF)
option(WITH_CODEC_MP3 "USE_CODEC_MP3" OFF)
option(WITH_CODEC_OPUS "USE_CODEC_OPUS" OFF)
option(WITH_CODEC_UMX "USE_CODEC_UMX" OFF)
option(WITH_CODEC_VORBIS "USE_CODEC_VORBIS" OFF)
option(WITH_CODEC_WAVE "USE_CODEC_WAVE" ON)
option(WITH_CODEC_XMP "USE_CODEC_XMP" OFF)

set(CMAKE_CXX_STANDARD 11)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu99 -Wall -Wno-trigraphs")
set(CMAKE_C_FLAGS_RELEASE "-O2 -frename-registers -fweb -DNDEBUG")

if (CMAKE_BUILD_TYPE STREQUAL Debug)
    add_definitions(-D_DEBUG)
endif ()

add_definitions(-DLINUX)
add_definitions(-DUSE_SDL2)

if (PLATFORM STREQUAL x11)
    add_definitions(-DVK_USE_PLATFORM_XCB_KHR)
    set(PLATFORM_LIBRARIES X11-xcb)
endif ()
if (PLATFORM STREQUAL wayland)
    add_definitions(-DVK_USE_PLATFORM_WAYLAND_KHR)
endif ()
if (WITH_CODEC_FLAC)
    add_definitions(-DUSE_CODEC_FLAC)
endif ()
if (WITH_CODEC_MIKMOD)
    add_definitions(-DUSE_CODEC_MIKMOD)
endif ()
if (WITH_CODEC_MODPLUG)
    add_definitions(-DUSE_CODEC_MODPLUG)
endif ()
if (WITH_CODEC_MP3)
    add_definitions(-DUSE_CODEC_MP3)
endif ()
if (WITH_CODEC_OPUS)
    add_definitions(-DUSE_CODEC_OPUS)
endif ()
if (WITH_CODEC_UMX)
    add_definitions(-DUSE_CODEC_UMX)
endif ()
if (WITH_CODEC_VORBIS)
    add_definitions(-DUSE_CODEC_VORBIS)
endif ()
if (WITH_CODEC_WAVE)
    add_definitions(-DUSE_CODEC_WAVE)
endif ()
if (WITH_CODEC_XMP)
    add_definitions(-DUSE_CODEC_XMP)
endif ()

# Dependencies

#find_package(mad REQUIRED)
#include_directories(${MAD_INCLUDE_DIR})

#find_package(Ogg REQUIRED)
#include_directories(${OGG_INCLUDE_DIR})

find_package(SDL2 REQUIRED)
include_directories(${SDL2_INCLUDE_DIRS})

#find_package(Vorbis REQUIRED)
#include_directories(${VORBIS_INCLUDE_DIR})
#message("VORB ${VORBIS_INCLUDE_DIR}")

#find_package(VorbisFile REQUIRED)
#include_directories(${VORBISFILE_INCLUDE_DIR})

find_package(X11 REQUIRED)
include_directories(${X11_INCLUDE_DIR})

# Source Files

set(CD_SOURCES
        Quake/cd_sdl.c)

set(GAME_SOURCES
        Quake/cfgfile.c
        Quake/chase.c
        Quake/cmd.c
        Quake/common.c
        Quake/console.c
        Quake/cvar.c
        Quake/host.c
        Quake/host_cmd.c
        Quake/keys.c
        Quake/menu.c
        Quake/sbar.c
        Quake/view.c
        Quake/wad.c
        Quake/world.c
        Quake/zone.c)

file(GLOB GAME2_SOURCES
        "Quake/cl_*.c"
        "Quake/sv_*.c")

file(GLOB GL_SOURCES
        "Quake/image.c"
        "Quake/gl_*.c"
        "Quake/r_*.c")

file(GLOB IN_SOURCES
        "Quake/in_*.c")

set(NET_SOURCES
        "Quake/net_bsd.c"
        "Quake/net_dgrm.c"
        "Quake/net_loop.c"
        "Quake/net_main.c"
        "Quake/net_udp.c")

set(PL_SOURCES
        "Quake/pl_linux.c"
        "Quake/sys_sdl_unix.c")

file(GLOB PR_SOURCES
        "Quake/pr_*.c")

file(GLOB SHADER_SOURCES
        "Shaders/Compiled/*.c")

set(SND_SOURCES
        Quake/bgmusic.c
        Quake/snd_codec.c
        Quake/snd_dma.c
        Quake/snd_flac.c
        Quake/snd_mem.c
        Quake/snd_mikmod.c
        Quake/snd_mix.c
        Quake/snd_modplug.c
        Quake/snd_opus.c
        Quake/snd_sdl.c
        Quake/snd_umx.c
        Quake/snd_wave.c
        Quake/snd_xmp.c)

list(REMOVE_ITEM SND_SOURCES "Quake/snd_mpg123.c")

set(UTIL_SOURCES
        "Quake/crc.c"
        "Quake/mathlib.c"
        "Quake/strlcat.c"
        "Quake/strlcpy.c")

# Target

add_executable(vkquake
        Quake/main_sdl.c
        ${CD_SOURCES}
        ${GAME_SOURCES}
        ${GAME2_SOURCES}
        ${GL_SOURCES}
        ${IN_SOURCES}
        ${NET_SOURCES}
        ${PL_SOURCES}
        ${PR_SOURCES}
        ${SHADER_SOURCES}
        ${SND_SOURCES}
        ${UTIL_SOURCES})

target_link_libraries(vkquake
        m
        libvulkan.so.1
        ${PLATFORM_LIBRARIES}
        ${MAD_LIBRARIES}
        ${OGG_LIBRARIES}
        ${SDL2_LIBRARIES}
        ${VORBIS_LIBRARIES}
        ${VORBISFILE_LIBRARIES})

if (CMAKE_BUILD_TYPE STREQUAL Release)
    add_custom_command(TARGET vkquake
            COMMAND strip vkquake
            COMMAND size vkquake)
endif ()
