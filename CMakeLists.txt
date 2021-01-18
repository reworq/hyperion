# Hyperion: runtime encrypter for 32/64 bit portable executables.
# Copyright (c) 2021-Infinity Reworq Team (only CI build scripts)
# Licensed under MIT license. See the LICENSE.txt file.

cmake_minimum_required(VERSION 3.18)

if(EXISTS "${CMAKE_TOOLCHAIN_FILE}")
    message(STATUS "Using CMake toolchain file: ${CMAKE_TOOLCHAIN_FILE}")
endif()

project(
    hyperion
    DESCRIPTION "runtime encrypter for 32/64 bit portable executables"
    HOMEPAGE_URL "${APPHELPER_HOMEPAGE_URL}"
    LANGUAGES C CXX
    VERSION 2.3.1.0
)

message(STATUS "CMAKE_C_COMPILER: ${CMAKE_C_COMPILER}")
message(STATUS "CMAKE_CXX_COMPILER: ${CMAKE_CXX_COMPILER}")
message(STATUS "CMAKE_MAKE_PROGRAM: ${CMAKE_MAKE_PROGRAM}")
message(STATUS "CMAKE_SYSTEM_NAME: ${CMAKE_SYSTEM_NAME}")
message(STATUS "CMAKE_SIZEOF_VOID_P: ${CMAKE_SIZEOF_VOID_P}")
message(STATUS "CMAKE_CXX_COMPILER_ID: ${CMAKE_CXX_COMPILER_ID}")

# Target arch
if("${CMAKE_SIZEOF_VOID_P}" STREQUAL "4")
    # 32 bit
    set(PROJECT_OS_ARCH "x86")
else()
    # 64 bit
    set(PROJECT_OS_ARCH "x86_64")
endif()
# Target compiler ID
if("${CMAKE_CXX_COMPILER_ID}" MATCHES "GNU")
    set(PROJECT_OS_COMPILER "gcc") # most likely MinGW
elseif("${CMAKE_CXX_COMPILER_ID}" MATCHES "MSVC")
    set(PROJECT_OS_COMPILER "msvc") # Microsoft Visual Studio
else()
    set(PROJECT_OS_COMPILER "unknown")
endif()
# Target platform name
string(TOLOWER "${CMAKE_SYSTEM_NAME}" PROJECT_OS_NAME)

include(FetchContent)
set(FETCHCONTENT_BASE_DIR "${PROJECT_BINARY_DIR}/CMakeFetchContent" CACHE PATH "FetchContent" FORCE)

FetchContent_Declare(
    nullsecuritynet_tools
    GIT_REPOSITORY "https://github.com/nullsecuritynet/tools"
    GIT_TAG "f4c2cdd"
    GIT_SHALLOW FALSE
    CONFIGURE_COMMAND ""
)
FetchContent_GetProperties(nullsecuritynet_tools)
if(NOT nullsecuritynet_tools_POPULATED)
    message(STATUS "Cloning nullsecuritynet/tools repo...")
    FetchContent_Populate(nullsecuritynet_tools)
    message(STATUS "Nullsecuritynet tools repo populated!")
endif()
message(STATUS "Nullsecuritynet tools build dir: ${nullsecuritynet_tools_BINARY_DIR}")
message(STATUS "Nullsecuritynet tools source dir: ${nullsecuritynet_tools_SOURCE_DIR}")

set(HYPERION_BINARY_DIR "${nullsecuritynet_tools_BINARY_DIR}/binary/hyperion/source")
set(HYPERION_SOURCE_DIR "${nullsecuritynet_tools_SOURCE_DIR}/binary/hyperion/source")
message(STATUS "HYPERION_BINARY_DIR: ${HYPERION_BINARY_DIR}")
message(STATUS "HYPERION_SOURCE_DIR: ${HYPERION_SOURCE_DIR}")

add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/src")
add_subdirectory("${CMAKE_CURRENT_LIST_DIR}/tests")

set(CPACK_GENERATOR "ZIP")
set(CPACK_PACKAGE_DIRECTORY "${CMAKE_CURRENT_BINARY_DIR}/dist")
set(CPACK_PACKAGE_FILE_INFO "${PROJECT_OS_NAME}-${PROJECT_OS_COMPILER}-${PROJECT_OS_ARCH}")
set(CPACK_PACKAGE_FILE_NAME "${PROJECT_NAME}-v${PROJECT_VERSION}-${CPACK_PACKAGE_FILE_INFO}")
set(CPACK_INCLUDE_TOPLEVEL_DIRECTORY FALSE)
include(CPack)
