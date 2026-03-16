# --------------------------------------------------------------------------------------
# Copyright (c) 2013-2026, Nucleic Development Team.
#
# Distributed under the terms of the Modified BSD License.
#
# The full license is in the file LICENSE, distributed with this software.
# --------------------------------------------------------------------------------------

cmake_minimum_required(VERSION 3.15)

project(kiwi 
    VERSION 1.5.0
    LANGUAGES CXX
    DESCRIPTION "A fast implementation of the Cassowary constraint solver"
)

# Determine if this is the top-level project
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    set(KIWI_IS_TOP_LEVEL TRUE)
else()
    set(KIWI_IS_TOP_LEVEL FALSE)
endif()

# Options
option(KIWI_BUILD_BENCHMARKS "Build benchmarks" ${KIWI_IS_TOP_LEVEL})
option(KIWI_BUILD_TESTS "Build tests" ${KIWI_IS_TOP_LEVEL})
option(KIWI_GENERATE_VERSION_HEADER "Generate version header" ${KIWI_IS_TOP_LEVEL})

# C++ standard
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Header-only library
add_library(kiwi INTERFACE)
add_library(kiwi::kiwi ALIAS kiwi)

target_include_directories(kiwi
    INTERFACE
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
        $<INSTALL_INTERFACE:include>
)

target_compile_features(kiwi INTERFACE cxx_std_11)

target_compile_options(kiwi INTERFACE
    $<$<CXX_COMPILER_ID:GNU,Clang,AppleClang>:
        -Wall 
        -Wextra
        -Wpedantic
    >
)

# Benchmarks
if(KIWI_BUILD_BENCHMARKS)
    add_subdirectory(benchmarks)
endif()

# Tests
if(KIWI_BUILD_TESTS)
    add_subdirectory(tests)
endif()

# Version header
if(KIWI_GENERATE_VERSION_HEADER)
    configure_file(
        ${CMAKE_CURRENT_SOURCE_DIR}/kiwi/version.h.in
        ${CMAKE_CURRENT_BINARY_DIR}/generated/version.h
    )
    target_include_directories(kiwi INTERFACE ${CMAKE_CURRENT_BINARY_DIR}/generated)
endif()

# Print configuration summary
message(STATUS "")
message(STATUS "Kiwi Configuration Summary:")
message(STATUS "  Version: ${PROJECT_VERSION}")
message(STATUS "  C++ Standard: C++${CMAKE_CXX_STANDARD}")
message(STATUS "  Build benchmarks: ${KIWI_BUILD_BENCHMARKS}")
message(STATUS "  Build tests: ${KIWI_BUILD_TESTS}")
message(STATUS "")
