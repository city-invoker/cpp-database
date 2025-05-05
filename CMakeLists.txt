#
#
# Tencent is pleased to support the open source community by making tRPC available.
#
# Copyright (C) 2024 THL A29 Limited, a Tencent company.
# All rights reserved.
#
# If you have downloaded a copy of the tRPC source code from Tencent,
# please note that tRPC source code is licensed under the GNU General Public License Version 2.0 (GPLv2),
# A copy of the GPLv2 License is included in this file.
#
#

cmake_minimum_required(VERSION 3.14)

project(trpc-cpp-database)

set(CMAKE_VERBOSE_MAKEFILE ON)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_BUILD_TYPE DEBUG)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-sign-compare -Wno-error=maybe-uninitialized")
set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

set(TRPC_INSTALL_PATH "/usr/local/trpc-cpp/trpc")
set(TRPC_INCLUDE_PATHS "${TRPC_INSTALL_PATH}/include/")

include(${TRPC_INSTALL_PATH}/cmake/config/trpc_config.cmake)
include(${TRPC_INSTALL_PATH}/cmake/tools/trpc_utils.cmake)

message("Fetching mysql libs, please waiting...")
include(mysqlclient)

file(GLOB_RECURSE SRC_FILES ./trpc/client/*.cc)
file(GLOB_RECURSE TEST_FILES ./trpc/client/*test.cc)
list(REMOVE_ITEM SRC_FILES ${TEST_FILES})

set(TARGET "trpc_cpp_database_mysql")
add_library(${TARGET} STATIC ${SRC_FILES})

target_link_directories(${TARGET} PUBLIC "${TRPC_INSTALL_PATH}/lib/")
target_link_libraries(${TARGET} PUBLIC trpc ${TARGET_LINK_LIBS})
target_include_directories(
    ${TARGET} PUBLIC
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${TRPC_INCLUDE_PATHS}
    ${MYSQLCLIENT_INCLUDE_DIR}
)

#install
install(
    TARGETS ${TARGET}
    ARCHIVE DESTINATION ${TRPC_INSTALL_PATH}/lib
)

install(
    FILES ${CMAKE_CURRENT_SOURCE_DIR}/third_party/mysqlclient/lib/libmysqlclient.a
    DESTINATION ${TRPC_INSTALL_PATH}/lib
)

install(
    DIRECTORY trpc/client/mysql/
    DESTINATION ${TRPC_INCLUDE_PATHS}/trpc/client/mysql
    FILES_MATCHING PATTERN "*.h"
)

install(
    DIRECTORY third_party/mysqlclient/include/
    DESTINATION ${TRPC_INCLUDE_PATHS}/mysqlclient
    FILES_MATCHING PATTERN "*"
)
