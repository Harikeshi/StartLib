#-----------------------------------------------
project(StartLib)
#-----------------------------------------------
cmake_minimum_required(VERSION 3.13)
#-----------------------------------------------
set(CORE_MAJOR_VERSION 1)
set(CORE_MINOR_VERSION 0)
set(CORE_PATCH_VERSION 0)
#-----------------------------------------------
cmake_policy(SET CMP0074 NEW)
#-----------------------------------------------
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
#-----------------------------------------------
option(BUILD_TESTS "Build test wirth gtest" ON)
#-----------------------------------------------
set(CMAKE_DEBUG_POSTFIX "_d")
#-----------------------------------------------
file(GLOB_RECURSE HEADERS "${CMAKE_CURRENT_SOURCE_DIR}/include/*.hpp")
file(GLOB_RECURSE SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/*.hpp")
#-----------------------------------------------
add_library(${PROJECT_NAME} STATIC ${HEADERS} ${SOURCES})
#-----------------------------------------------
target_link_libraries(${PROJECT_NAME} geos)
#-----------------------------------------------
target_include_directories(${PROJECT_NAME}
PUBLIC
"${CMAKE_CURRENT_SOURCE_DIR}/include"
PRIVATE
"${CMAKE_CURRENT_SOURCE_DIR}/src")
#-----------------------------------------------
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/CMakeTools/")
if(CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++14 -Wall -Wextra -pthread -fext-numeric-literals")
elseif(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -W4 /bigobj")
    set(CMAKE_CXX_STANDARD 14)
endif()
#-----------------------------------------------
#include(GroupingTools)
include(CopyResources)
#-----------------------------------------------
find_package(Git)
#-----------------------------------------------
if(${BUILD_TESTS})
    target_include_directories(${PROJECT_NAME} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/src")
    add_subdirectory(tests/)
endif()
#-----------------------------------------------
if(NOT ${GTEST_FOUND})
    mark_as_advanced(
    gmock_build_tests
    gtest_build_samples
    gtest_build_tests
    gtest_disable_pthreads
    gtest_force_shared_crt
    )
endif()
#-----------------------------------------------
if(${BUILD_TESTS})
    enable_testing()
    message(STATUS "[${PROJECT_NAME}] Build tests: ON")
else()
    message(STATUS "[${PROJECT_NAME}] Build tests: OFF")
endif()
#-----------------------------------------------
add_subdirectory(3rdparty/)
#-----------------------------------------------
