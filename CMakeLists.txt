# Genesis - A toolkit for working with phylogenetic data.
# Copyright (C) 2014-2018 Lucas Czech, Pierre Barbera
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#
# Contact:
# Lucas Czech <lucas.czech@h-its.org>
# Exelixis Lab, Heidelberg Institute for Theoretical Studies
# Schloss-Wolfsbrunnenweg 35, D-69118 Heidelberg, Germany

# ------------------------------------------------------------------------------
#   Build Options
# ------------------------------------------------------------------------------

get_filename_component( THIS_DIR_NAME ${CMAKE_CURRENT_LIST_DIR} NAME)

set( EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin/apps/${THIS_DIR_NAME} )

# Set rpath for all targets, so that they work out of the box (libgenesis.so in parent directory),
# as well as when relocating with the lib being in the same directory.
# Also, we need to set two versions of ORIGIN here (one and two dollar signs).
# This is because on some systems it seems to be escaped, on some not...
# See https://cmake.org/pipermail/cmake/2008-January/019290.html
# and https://cmake.org/Wiki/CMake_RPATH_handling for details.
# Tipp: Use `ldd binary` and `readelf -d binary` to check RPATH settings.
set( CMAKE_INSTALL_RPATH "$ORIGIN/../..:$$ORIGIN/../..:$ORIGIN/..:$$ORIGIN/..:$ORIGIN:$$ORIGIN" )
set( CMAKE_BUILD_WITH_INSTALL_RPATH TRUE )

# ------------------------------------------------------------------------------
#   Sources
# ------------------------------------------------------------------------------

# Find all source files in this directory and compile them into individual binaries, each
# linked to the shared lib. All files should thus contain their own main function.
file( GLOB genesis_apps_sources *.cpp )

include_directories( ${PROJECT_SOURCE_DIR}/lib )

# ------------------------------------------------------------------------------
#   Build Applications
# ------------------------------------------------------------------------------

if( genesis_apps_sources )

    foreach( app_src ${genesis_apps_sources} )
        get_filename_component( app_name ${app_src} NAME_WE )
        message( STATUS "  ${ColorBlue}${app_name}${ColorEnd}" )

        add_executable( ${app_name} ${app_src} )

        target_link_libraries( ${app_name} genesis_lib_shared )

        # Link against any external libraries, e.g. Pthreads.
        target_link_libraries (${app_name} ${GENESIS_INTERNAL_LINK_LIBRARIES})

    endforeach()

else()

    message( STATUS "No genesis applications found" )

endif()
