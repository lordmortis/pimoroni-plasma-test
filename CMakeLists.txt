cmake_minimum_required(VERSION 3.12)

# Change your executable name to something creative!
set(NAME ledcontroller) # <-- Name your project/executable here!

if (DEFINED ENV{PICO_SDK_PATH} AND (NOT PICO_SDK_PATH))
    set(PICO_SDK_PATH $ENV{PICO_SDK_PATH})
    message("Using PICO_SDK_PATH from environment ('${PICO_SDK_PATH}')")
endif ()


#set(PICO_SDK_PATH "/Users/lordmortis/Projects/pico/pico-sdk")

include(pimoroni_pico_import.cmake)
include(pico_sdk_import.cmake)

# Gooey boilerplate
project(${NAME} C CXX ASM)
set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

# Initialize the SDK
pico_sdk_init()

# Add your source files
add_executable(${NAME}
    main.cpp # <-- Add source files here!
)

# Include required libraries
# This assumes `pimoroni-pico` is stored alongside your project
include(common/pimoroni_i2c)
include(drivers/plasma/plasma)
include(drivers/ioexpander/ioexpander)
include(drivers/rgbled/rgbled)
include(drivers/button/button)
include(libraries/plasma2040/plasma2040)
include(libraries/pico_display/pico_display)
include(libraries/breakout_encoder/breakout_encoder)

# Don't forget to link the libraries you need!
target_link_libraries(${NAME}
    pico_stdlib
    plasma2040
    breakout_encoder
    rgbled
    button
)

# create map/bin/hex file etc.
pico_add_extra_outputs(${NAME})

pico_enable_stdio_usb(${NAME} 1)

# Set up files for the release packages
install(FILES
    ${CMAKE_CURRENT_BINARY_DIR}/${NAME}.uf2
    ${CMAKE_CURRENT_LIST_DIR}/README.md
    DESTINATION .
)

set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY OFF)
set(CPACK_GENERATOR "ZIP" "TGZ")
include(CPack)
