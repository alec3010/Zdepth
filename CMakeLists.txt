cmake_minimum_required(VERSION 3.5)
project(zdepth LANGUAGES CXX)
include("cmake/zdepth-config-version.cmake")
set(CMAKE_CXX_STANDARD 11)


################################################################################
# Build Options

set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "" FORCE)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release)
endif()

# In debug mode, add -DDEBUG
add_compile_options("$<$<CONFIG:DEBUG>:-DDEBUG>")

if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W4")
else()
    # Warnings
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -pedantic")

    # Remove Asio warning
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-unused-local-typedefs")

    # Static library: -fPIC
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC")

    set(CMAKE_CXX_FLAGS_DEBUG "-g -O0 -march=native -fstack-protector")
    set(CMAKE_CXX_FLAGS_RELEASE "-O3 -march=native")
endif()


################################################################################
# Subprojects

if (NOT TARGET zstd2)
    add_subdirectory(zstd zstd)
endif()


################################################################################
# Source

set(INCLUDE_FILES
    include/zdepth.hpp
)

set(SOURCE_FILES
    ${INCLUDE_FILES}
    src/zdepth.cpp
)

include_directories(include)


################################################################################
# Targets

# zdepth library

add_library(zdepth STATIC ${SOURCE_FILES})
add_library(zdepth::zdepth ALIAS zdepth)

target_link_libraries(zdepth PUBLIC zstd2)

target_include_directories(zdepth PUBLIC
    "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>"
    "$<INSTALL_INTERFACE:include>"
)

# zdepth test application


################################################################################
# Install

install(FILES LICENSE DESTINATION ".")
install(FILES README.md DESTINATION ".")
install(FILES ${INCLUDE_FILES} DESTINATION include)


