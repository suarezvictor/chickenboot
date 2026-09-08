cmake_minimum_required(VERSION 3.13)
include($ENV{PICO_SDK_PATH}/external/pico_sdk_import.cmake)
pico_sdk_init()

project(main C CXX ASM)
add_executable(main main.c)
target_link_libraries(main pico_stdlib)
pico_add_extra_outputs(main)


