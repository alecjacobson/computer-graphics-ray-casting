cmake_minimum_required(VERSION 2.6)
project(raycasting)

# Prepare the build environment
set(ROOT "${CMAKE_CURRENT_SOURCE_DIR}")
option(USE_SOLUTION "Use solution"  OFF)
# Add your project files
include_directories("include/")
if(USE_SOLUTION)
  set(SRC_DIR "solution/")
else()
  set(SRC_DIR "src/")
endif()
file(GLOB SRCFILES "${SRC_DIR}/*.cpp")
list(APPEND SRCFILES main.cpp)
# create executable
include(CheckCXXCompilerFlag)

CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
    message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

if(HW2LIB_DIR)
  link_directories(${HW2LIB_DIR})
endif()

add_executable(${PROJECT_NAME} ${SRCFILES})
target_include_directories(${PROJECT_NAME} SYSTEM PUBLIC ${ROOT}/eigen)
