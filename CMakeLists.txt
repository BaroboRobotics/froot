cmake_minimum_required(VERSION 2.8)
project(froot)

include(${PROJECT_SOURCE_DIR}/firmware/arm_macros.cmake)

set(BUILD_SHARED_LIBS OFF)

if (NOT DEFINED CMAKE_TOOLCHAIN_FILE)
    message(FATAL_ERROR "CMAKE_TOOLCHAIN_FILE must be defined, see README.md")
endif()

set(VERSION_MAJOR 4)
set(VERSION_MINOR 0)
set(VERSION_PATCH 0)
set(VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH})

add_definitions("-DPHY_ATMEGA128RFA1 -DHAL_ATMEGA128RFA1"
                "-DPLATFORM_RCB128RFA1 -DF_CPU=16000000L")
set(CMAKE_C_FLAGS "-Wall -std=gnu99 -O2 -fno-zero-initialized-in-bss -fdata-sections -ffunction-sections -funsigned-char -funsigned-bitfields -mmcu=atmega128rfa1")
set(CMAKE_CXX_FLAGS "-Wall -std=c++11 -O2 -fno-zero-initialized-in-bss -fdata-sections -ffunction-sections -funsigned-char -funsigned-bitfields -mmcu=atmega128rfa1")
set(CMAKE_EXE_LINKER_FLAGS "-Wl,--gc-sections -mmcu=atmega128rfa1 -Wl,-u,vfprintf")

add_subdirectory(ribbon-bridge)
add_subdirectory(interfaces)
add_subdirectory(libsfp)
add_subdirectory(LwMesh)
add_subdirectory(firmware)
