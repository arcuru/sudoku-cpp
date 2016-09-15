# Basic CMake project
cmake_minimum_required(VERSION 2.8.11)

# Output compilation database for clangtidy
set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

# Set default build type to Release
if (NOT CMAKE_BUILD_TYPE)
    message(STATUS "No build type selected, default to Release.")
    set(CMAKE_BUILD_TYPE "Release")
endif()

set(proj sudoku)

# Name the project
project(${proj} CXX)

# Make all warnings show up no matter which type of target
#set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -Wall -Werror -Og")
#set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -Wall")

# Enable C++14 features on gcc/clang
if("${CMAKE_CXX_COMPILER_ID}" MATCHES "(Clang)")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Weverything -Wno-c++98-compat -Wno-padded -Wno-exit-time-destructors -Wno-global-constructors -std=c++14")
endif()

if("${CMAKE_CXX_COMPILER_ID}" MATCHES "(GCC)")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Werror -std=c++14")
endif()

set(proj_cpp "sudoku.cpp")

add_executable(${proj} ${proj_cpp})

# Run test on every build
#add_custom_command(TARGET ${proj} POST_BUILD COMMAND 
#    ${proj} < ${CMAKE_CURRENT_SOURCE_DIR}/data/super.txt)

# Unit Tests?
if(EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${proj}_test.cpp)
    # Build executable from sources and headers
    add_executable(${proj}_test ${proj_cpp} ${proj_hpp} ${proj}_test.cpp)

    # We need boost includes
    target_include_directories(${proj}_test PRIVATE ${Boost_INCLUDE_DIRS} ..)
    
    # We need boost libraries
    target_link_libraries(${proj}_test ${Boost_LIBRARIES})

    # Run the tests on every build
    add_custom_command(TARGET ${proj}_test POST_BUILD COMMAND ${proj}_test)

endif()

# Benchmarks?
if(EXISTS ${CMAKE_CURRENT_SOURCE_DIR}/${proj}_benchmark.cpp)

    # Add benchmark executable
    add_executable(${proj}_benchmark ${proj_cpp} ${proj_hpp} ${proj}_benchmark.cpp)

    # We need boost includes
    target_include_directories(${proj}_benchmark PRIVATE ${Boost_INCLUDE_DIRS} ..)

    # We need boost libraries
    target_link_libraries(${proj}_benchmark ${Boost_LIBRARIES})

endif()


