cmake_minimum_required(VERSION 3.23)
project(hbl VERSION 2.4.4)

set(CMAKE_C_STANDARD 17)
set(CMAKE_CXX_STANDARD 23)
enable_language(ASM C CXX)

# We have to add the cmake directory to have access to
# the Switch tools and also find_package for libnx.
list(APPEND CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/cmake)

# SwitchTools provide various helper functions for building Switch file formats.
include(SwitchTools)

# Find libnx, which is required by our application.
find_package(LIBNX REQUIRED)

## Project sources
include_directories(${PROJECT_SOURCE_DIR}/include/)
file(GLOB_RECURSE SOURCES_ASM ${PROJECT_SOURCE_DIR}/source/*.s)
file(GLOB_RECURSE SOURCES_C ${PROJECT_SOURCE_DIR}/source/*.c)
file(GLOB_RECURSE SOURCES_CXX ${PROJECT_SOURCE_DIR}/source/*.cpp)

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -DVERSION=\"v${PROJECT_VERSION}\"")

add_executable(hbl ${SOURCES_ASM} ${SOURCES_C} ${SOURCES_CXX})

# Next, we need to set the required target properties
# so CMake can detect the JSON configuration for the module.
set_target_properties(hbl PROPERTIES
        CONFIG_JSON ${PROJECT_SOURCE_DIR}/sysmodule.json)

# Then, we link the required libraries (libnx) against the executable.
target_link_libraries(hbl switch::libnx)

# And finally we can add the actual binary (.nsp) target.
add_nsp_target(hbl)
