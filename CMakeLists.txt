# Copyright (c) 2016-2018 ZhuoShi

cmake_minimum_required(VERSION 3.10.0)

# ref: https://blog.kitware.com/cmake-and-the-default-build-type/
# Set a default build type if none was specified
set(default_build_type "RelWithDebInfo")

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    message(STATUS "Setting build type to '${default_build_type}' as none was specified.")
    set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
            STRING "Choose the type of build." FORCE)
    # Set the possible values of build type for cmake-gui
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
            "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

project(hello)
#
#----------------------------- Utils -------------------------------------------

function(simple_executable name)
    set(srcs ${name}.cc)

    foreach(item ${ARGN})
        if (${item} MATCHES ".*\\.cc$")
            set(srcs ${srcs} ${item})
        endif()
    endforeach(item)

    add_executable(${name} ${srcs})

    foreach(item ${ARGN})
        if (NOT (${item} MATCHES ".*\\.cc$"))
            target_link_libraries(${name} ${item})
        endif()
    endforeach(item)
endfunction(simple_executable)

function(simple_gtest name)
    set(srcs ${name}.test.cc)

    foreach(item ${ARGN})
        if (${item} MATCHES ".*\\.cc$")
            set(srcs ${srcs} ${item})
        endif()
    endforeach(item)

    add_executable(${name}.test ${srcs})

    foreach(item ${ARGN})
        if (NOT (${item} MATCHES ".*\\.cc$"))
            target_link_libraries(${name}.test ${item})
        endif()
    endforeach(item)

    target_link_libraries(${name}.test ${GTEST_BOTH_LIBRARIES})

    add_test(${name} ${name}.test)
endfunction(simple_gtest)

#-------------------------------------------------------------------------------

set(CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_STANDARD 11)

#------------------------- building flags --------------------------------------

# Make with argument {VERBOSE=1} to see the compiler and flags really in use.
set(CMAKE_C_FLAGS "-Wall -Wextra")
set(CMAKE_C_FLAGS_DEBUG "-g -O0")
set(CMAKE_C_FLAGS_RELEASE "-DNDEBUG -O3 -march=core-avx2")
set(CMAKE_C_FLAGS_RELWITHDEBINFO "-g -DNDEBUG -O3 -march=core-avx2")

set(CMAKE_CXX_FLAGS "-Wall -Wextra")
set(CMAKE_CXX_FLAGS_DEBUG "-g -O0")
set(CMAKE_CXX_FLAGS_RELEASE "-DNDEBUG -O3 -march=core-avx2")
set(CMAKE_CXX_FLAGS_RELWITHDEBINFO "-g -DNDEBUG -O3 -march=core-avx2")

# strip in release mode
set(CMAKE_EXE_LINKER_FLAGS "-rdynamic")

set(CMAKE_EXE_LINKER_FLAGS_RELEASE "-s")
set(CMAKE_SHARED_LINKER_FLAGS_RELEASE "-s")
set(CMAKE_MODULE_LINKER_FLAGS_RELEASE "-s")
set(CMAKE_STATIC_LINKER_FLAGS_RELEASE "-s")

include_directories(${PROJECT_SOURCE_DIR})

add_subdirectory(src)

