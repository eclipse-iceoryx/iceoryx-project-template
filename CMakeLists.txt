# Copyright (c) 2022 by Apex.AI Inc. All rights reserved.
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
#
# SPDX-License-Identifier: Apache-2.0

cmake_minimum_required(VERSION 3.16)

# check if iceoryx is in CMAKE_PREFIX_PATH
find_package(iceoryx_hoofs QUIET)
find_package(iceoryx_posh QUIET)

# Link iceoryx_hoofs and iceoryx_posh libraries statically
option(BUILD_SHARED_LIBS "Link libraries dynamically" OFF)

# fetch iceoryx if not found
if(NOT iceoryx_hoofs_FOUND OR NOT iceoryx_posh_FOUND)
    if(iceoryx_hoofs_FOUND)
        message(FATAL_ERROR "iceoryx_hoofs was not found with 'find_package' but other parts were found!")
    endif()
    if(iceoryx_posh_FOUND)
        message(FATAL_ERROR "iceoryx_posh was not found with 'find_package' but other parts were found!")
    endif()

    include(FetchContent)
    FetchContent_Declare(
        iceoryx
        GIT_REPOSITORY https://github.com/eclipse-iceoryx/iceoryx.git
        GIT_TAG v2.0.2
    )
    FetchContent_GetProperties(iceoryx)
    if (NOT iceoryx_POPULATED)
        message(STATUS "updating: iceoryx" )
        FetchContent_Populate(iceoryx)
    endif()

    set(ICEORYX_WITH_FETCH_CONTENT true CACHE INTERNAL "")
    set(iceoryx_SOURCE_DIR ${iceoryx_SOURCE_DIR} CACHE INTERNAL "")
    set(iceoryx_BINARY_DIR ${iceoryx_BINARY_DIR} CACHE INTERNAL "")
endif()

if(ICEORYX_WITH_FETCH_CONTENT)
    add_subdirectory(${iceoryx_SOURCE_DIR}/iceoryx_hoofs ${iceoryx_BINARY_DIR}/iceoryx_hoofs)
    add_subdirectory(${iceoryx_SOURCE_DIR}/iceoryx_posh  ${iceoryx_BINARY_DIR}/iceoryx_posh)

    find_package(iceoryx_posh REQUIRED)
    find_package(iceoryx_hoofs REQUIRED)
endif()

include(IceoryxPlatform)

project(iceoryx_project_template)

add_executable(publisher src/publisher.cpp)
target_link_libraries(publisher
    iceoryx_hoofs::iceoryx_hoofs
    iceoryx_posh::iceoryx_posh
)

add_executable(subscriber src/subscriber.cpp)
target_link_libraries(subscriber
    iceoryx_hoofs::iceoryx_hoofs
    iceoryx_posh::iceoryx_posh
)

set_target_properties(
    publisher
    subscriber
    PROPERTIES
    CXX_STANDARD_REQUIRED ON
    CXX_STANDARD ${ICEORYX_CXX_STANDARD}
    POSITION_INDEPENDENT_CODE ON
)

if(ICEORYX_WITH_FETCH_CONTENT)
message("
#############################################################
The project was build by obtaining iceoryx with FetchContent.
If you want to use an existing installation use
'-DCMAKE_PREFIX_PATH=/path/to/installed/iceoryx'!
#############################################################
")
endif()
