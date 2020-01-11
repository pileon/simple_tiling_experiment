# Copyright 2020 Joachim Pileborg
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required(VERSION 3.15)
project(simple_tiling_experiment
        VERSION 0.0.1
        DESCRIPTION "Simple experiment on writing a 2D tiling \"game\"")

set(CMAKE_CXX_STANDARD_REQUIRED YES)
set(CMAKE_CXX_EXTENSIONS NO)
set(CMAKE_CXX_STANDARD 20)

find_package(Threads)

set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREAD ON)

find_package(Boost 1.67 REQUIRED
        program_options)

find_package(SFML 2.5 REQUIRED COMPONENTS graphics window system)
if(NOT SFML_FOUND)
    message(FATAL_ERROR "SFML not found, required for this project (see https://www.sfml-dev.org)")
endif()

add_executable(simple_tiling_experiment main.cpp)

target_include_directories(simple_tiling_experiment PRIVATE ${CMAKE_SOURCE_DIR}/include Boost::headers ${BOX2D_INCLUDE_DIRS})
target_link_libraries(simple_tiling_experiment Threads::Threads Boost::program_options sfml-graphics)

if(${CLION_IDE})
    target_compile_definitions(simple_tiling_experiment PRIVATE -DCLION_IDE)
endif()

if(CMAKE_CXX_COMPILER_ID MATCHES "^(GNU)|(Clang)$")
    target_compile_options(simple_tiling_experiment PRIVATE -Wall -Wextra -Wpedantic -Wimplicit-fallthrough -Werror -pipe)
elseif(CMAKE_CXX_COMPILER_ID MATCHES "^MSVC$")
    target_compile_options(simple_tiling_experiment PRIVATE -W4)
else()
    message(WARNING "Unknown compiler, do not know how to test for specific flags")
endif()

if(CMAKE_CXX_COMPILER_ID MATCHES "^GNU$")
    target_link_libraries(simple_tiling_experiment stdc++fs)
endif()

if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_definitions(simple_tiling_experiment PRIVATE -DDEBUG)

    if(CMAKE_CXX_COMPILER_ID MATCHES "^(GNU)|(Clang)$")
        target_compile_options(simple_tiling_experiment PRIVATE -O0)
    endif()
else()
    target_compile_definitions(simple_tiling_experiment PRIVATE -DNDEBUG)
endif()
