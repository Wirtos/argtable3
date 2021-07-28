################################################################################
# This file is part of the argtable3 library.
#
# Copyright (C) 2016-2021 Tom G. Huang
# <tomghuang@gmail.com>
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#     * Redistributions of source code must retain the above copyright
#       notice, this list of conditions and the following disclaimer.
#     * Redistributions in binary form must reproduce the above copyright
#       notice, this list of conditions and the following disclaimer in the
#       documentation and/or other materials provided with the distribution.
#     * Neither the name of STEWART HEITMANN nor the  names of its contributors
#       may be used to endorse or promote products derived from this software
#       without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL STEWART HEITMANN BE LIABLE FOR ANY DIRECT,
# INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
# (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
# LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND
# ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
# SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
################################################################################

cmake_minimum_required(VERSION 3.12)

project(argtable3 LANGUAGES C)

option(BUILD_SHARED_LIBS "Build shared library" OFF)
option(ARGTABLE3_ENABLE_TESTS "Enable unit tests" ON)
option(ARGTABLE3_ENABLE_EXAMPLES "Enable examples" ON)
option(ARGTABLE3_ENABLE_ARG_REX_DEBUG "Enable arg_rex debug output" OFF)
option(ARGTABLE3_REPLACE_GETOPT "Replace getopt in the system C library" ON)
option(ARGTABLE3_LONG_ONLY "Use getopt_long_only instead of getopt_long" OFF)

set(PACKAGE_NAME "argtable3")

add_library(${PROJECT_NAME}
        src/arg_cmd.c
        src/arg_date.c
        src/arg_dbl.c
        src/arg_dstr.c
        src/arg_end.c
        src/arg_file.c
        src/arg_hashtable.c
        src/arg_int.c
        src/arg_lit.c
        src/arg_rem.c
        src/arg_rex.c
        src/arg_str.c
        src/arg_utils.c
        src/argtable3.c
        src/arg_getopt_long.c)


target_compile_definitions(${PROJECT_NAME}
        PRIVATE
        $<$<BOOL:${ARGTABLE3_ENABLE_ARG_REX_DEBUG}>:ARG_REX_DEBUG>
        $<$<NOT:$<BOOL:${ARGTABLE3_REPLACE_GETOPT}>>:ARG_REPLACE_GETOPT=0 REPLACE_GETOPT>
        $<$<BOOL:${ARGTABLE3_LONG_ONLY}>:ARG_LONG_ONLY>
        _XOPEN_SOURCE=700
        )


include(GenerateExportHeader)
generate_export_header(${PROJECT_NAME}
        EXPORT_MACRO_NAME "ARG_EXTERN"
        )

include(GNUInstallDirs)

target_include_directories(${PROJECT_NAME} PUBLIC
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>
        )


set_property(TARGET ${PROJECT_NAME}
        PROPERTY PUBLIC_HEADER
        src/${PROJECT_NAME}.h
        "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}_export.h"
        )


add_library(${PACKAGE_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

install(TARGETS ${PROJECT_NAME}
        EXPORT ${PACKAGE_NAME}Config
        COMPONENT ${PROJECT_NAME}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}/${PROJECT_NAME}
        LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}
        ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}/${PROJECT_NAME}
        PUBLIC_HEADER DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}
        )
export(TARGETS
        ${PROJECT_NAME}
        NAMESPACE ${PACKAGE_NAME}::
        FILE "${CMAKE_CURRENT_BINARY_DIR}/${PACKAGE_NAME}Config.cmake"
        )

install(EXPORT ${PACKAGE_NAME}Config
        DESTINATION "${CMAKE_INSTALL_DATADIR}/cmake"
        NAMESPACE ${PACKAGE_NAME}::
        )
