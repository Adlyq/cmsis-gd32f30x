cmake_minimum_required(VERSION 3.13)

set(GD32F30x_CMSIS_PATH CMSIS)
set(GD32F30x_PERIPHERAL_PATH GD32F30x_standard_peripheral)

# Peripheral Driver
file(GLOB GD32F30x_PERIPHERAL_SRCS ${GD32F30x_PERIPHERAL_PATH}/Source/*.c)

# CMSIS
set(TARGET_C_SOURCES
        ${GD32F30x_CMSIS_PATH}/GD/GD32F30x/Source/system_gd32f30x.c
        ${GD32F30x_PERIPHERAL_SRCS}
)

set(TARGET_C_INCLUDES
        ${GD32F30x_CMSIS_PATH}/GD/GD32F30x/Include
        ${GD32F30x_CMSIS_PATH}
        ${GD32F30x_PERIPHERAL_PATH}/Include
)

# Shared library and linker script search paths
set(TARGET_LIB_DIRECTORIES
        ${GD32F30x_CMSIS_PATH}/GD/GD32F30x/Include
        ${GD32F30x_PERIPHERAL_PATH}/Include
)

if (NOT TARGET_STARTUP_ASM)
    message(FATAL_ERROR "Must set TARGET_STARTUP_ASM to locate startup asm")
endif ()
message(STATUS "Use startup asm: " ${TARGET_STARTUP_ASM})

if (NOT TARGET_LD_SCRIPT)
    message(FATAL_ERROR "Must set TARGET_LD_SCRIPT to locate ld script")
endif ()
message(STATUS "Use LD script: " ${TARGET_LD_SCRIPT})

add_library(GD32F30x_SDK STATIC ${TARGET_C_SOURCES} ${TARGET_STARTUP_ASM})

# Shared sources, includes and definitions
target_compile_definitions(GD32F30x_SDK PUBLIC ${TARGET_C_DEFINES})
target_include_directories(GD32F30x_SDK
        SYSTEM PUBLIC ${TARGET_C_INCLUDES}
        SYSTEM INTERFACE ${TARGET_C_INCLUDES}
)

target_link_directories(GD32F30x_SDK PUBLIC ${TARGET_LIB_DIRECTORIES})
target_link_libraries(GD32F30x_SDK PUBLIC "c" "m" "nosys")
target_link_options(GD32F30x_SDK
        PUBLIC "-Wl,-Map=${CMAKE_CURRENT_BINARY_DIR}/${CMAKE_PROJECT_NAME}.map,--cref"
)
target_compile_options(GD32F30x_SDK PRIVATE -w)

target_link_libraries(BSP_LIB INTERFACE GD32F30x_SDK)
