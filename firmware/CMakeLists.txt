cmake_minimum_required(VERSION 3.20)

# Project name
project(FlightController C CXX ASM)

# Set standards
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# MCU settings (example: STM32F4)
set(MCU_FLAGS "-mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard")

# Compiler flags
set(CMAKE_C_FLAGS "${MCU_FLAGS} -Wall -O2 -ffast-math")
set(CMAKE_CXX_FLAGS "${MCU_FLAGS} -Wall -O2 -fno-exceptions -fno-rtti")
set(CMAKE_ASM_FLAGS "${MCU_FLAGS}")

# Linker script
set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/linker.ld)

# Linker flags
set(CMAKE_EXE_LINKER_FLAGS
    "${MCU_FLAGS} -T${LINKER_SCRIPT} -Wl,-Map=${PROJECT_NAME}.map -Wl,--gc-sections"
)

# Include directories
include_directories(
    Core/Inc
    Drivers/STM32F4xx_HAL_Driver/Inc
    Drivers/CMSIS/Include
    Drivers/CMSIS/Device/ST/STM32F4xx/Include
)

# Source files
file(GLOB_RECURSE SOURCES
    Core/Src/*.c
    Core/Src/*.cpp
    Drivers/STM32F4xx_HAL_Driver/Src/*.c
)

# Add executable
add_executable(${PROJECT_NAME}.elf ${SOURCES})

# Post-build: generate .bin and .hex
add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
    COMMAND arm-none-eabi-objcopy -O binary ${PROJECT_NAME}.elf ${PROJECT_NAME}.bin
    COMMAND arm-none-eabi-objcopy -O ihex ${PROJECT_NAME}.elf ${PROJECT_NAME}.hex
)

# Size info
add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
    COMMAND arm-none-eabi-size ${PROJECT_NAME}.elf
)