# ======================
# ARA API CMake Integration
#
# Copyright (c) 2020-2022, Celemony Software GmbH, All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ======================

cmake_minimum_required(VERSION 3.4 FATAL_ERROR)

include("${CMAKE_CURRENT_SOURCE_DIR}/ARA_API_Helpers.cmake")
include("${CMAKE_CURRENT_SOURCE_DIR}/ARA_Version.cmake")

project(ARA_API
    DESCRIPTION "ARA Audio Random Access: API specification"
    #only added in CMake 3.12:
    #HOMEPAGE_URL https://www.celemony.com/ara
    VERSION ${ARA_MAJOR_VERSION}.${ARA_MINOR_VERSION}.${ARA_PATCH_VERSION}.${ARA_BUILD_VERSION}
    LANGUAGES C CXX
)

option(ARA_ENABLE_DEPRECATION_WARNINGS "Enable warnings when using deprecated ARA APIs" OFF)

# ======================

set(ARA_API_Files
    "${CMAKE_CURRENT_SOURCE_DIR}/ARAInterface.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/ARAAudioFileChunks.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/ARAVST3.h"
    "${CMAKE_CURRENT_SOURCE_DIR}/ARA_API.pdf"
    "${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.txt"
    "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE.txt"
    "${CMAKE_CURRENT_SOURCE_DIR}/ARA_API_Helpers.cmake"
    "${CMAKE_CURRENT_SOURCE_DIR}/ARA_Version.cmake"
)
if(APPLE)
    list(INSERT ARA_API_Files 2
        "${CMAKE_CURRENT_SOURCE_DIR}/ARAAudioUnit.h"
    )
endif()

if(CMAKE_VERSION VERSION_GREATER_EQUAL 3.19)
    add_library(ARA_API INTERFACE ${ARA_API_Files})
else()
    # older CMake interface targets cannot have non-interface sources or properties,
    # so we need to use an empty object target instead
    add_library(ARA_API OBJECT ${ARA_API_Files})
    set_target_properties(ARA_API PROPERTIES
        LINKER_LANGUAGE NONE
    )
endif()

target_compile_features(ARA_API
    INTERFACE
        c_std_99
        cxx_std_11
)

ara_private_configure_ARA_API_interface_settings(ARA_API INTERFACE)

set_target_properties(ARA_API PROPERTIES
    XCODE_GENERATE_SCHEME OFF
)

ara_group_target_files(ARA_API)
