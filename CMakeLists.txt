#    Copyright (c) 2010-2019, Delft University of Technology
#    All rigths reserved
#
#    This file is part of the Tudat. Redistribution and use in source and
#    binary forms, with or without modification, are permitted exclusively
#    under the terms of the Modified BSD license. You should have received
#    a copy of the license with this file. If not, please or visit:
#    http://tudat.tudelft.nl/LICENSE.
#
#    Note that this license only applies to this CMakeLists.txt file, not the
#    nrlmsise-00 library itself
#

# Specify minimum CMake version.
cmake_minimum_required( VERSION 2.6 )

# Specific project name.
project( NRLMSISE00 )

# Detect if NRLMSISE00 is build standalone or from within project
if(${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_CURRENT_SOURCE_DIR})
  message(STATUS "Building ${PROJECT_NAME} standalone.")
  include("${CMAKE_CURRENT_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
else()
  message(STATUS "Building ${PROJECT_NAME} from within ${CMAKE_PROJECT_NAME}.")
  include("${CMAKE_CURRENT_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
  include("${CMAKE_SOURCE_DIR}/UserSettings.txt" OPTIONAL)
  set(PROJECTROOT ${PROJECTROOT} PARENT_SCOPE)
endif()

# Define the source directory and set custom bin and lib directories for building.
set(SRCROOT ${PROJECT_SOURCE_DIR})

set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${SRCROOT}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${SRCROOT}/lib)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${SRCROOT}/bin)

# Set the build flags
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -DINLINE")
set(CMAKE_C_FLAGS_DEBUG          "-g")
set(CMAKE_C_FLAGS_RELEASE        "-O2 -DNDEBUG")
set(CMAKE_C_FLAGS_RELWITHDEBINFO "-O2 -g")

# Set CMake build-type. If it not supplied by the user (either directly as an
# argument of through the "UserSettings.txt" file, the default built type is
# "Release".
if((NOT CMAKE_BUILD_TYPE) OR (CMAKE_BUILD_TYPE STREQUAL "Release"))
   message(STATUS "WARNING: building release version!")
   set(CMAKE_BUILD_TYPE Release)
elseif(CMAKE_BUILD_TYPE STREQUAL "Debug")
   message(STATUS "WARNING: building debug version!")
endif()

#Set the header and source files.
set(NRLMSISE00_HEADERS "${SRCROOT}/nrlmsise-00.h")
set(NRLMSISE00_SOURCES "${SRCROOT}/nrlmsise-00.c"
                       "${SRCROOT}/nrlmsise-00_data.c")

# Add static libraries.
add_library(nrlmsise00 STATIC ${NRLMSISE00_SOURCES} ${NRLMSISE00_HEADERS})

# Define option and build test executables
OPTION(NRLMSISE00_WITH_TESTS "Compile NRLMSISE-00 test executables" ON)
IF(NRLMSISE00_WITH_TESTS)
        enable_testing()
        add_executable(nrlmsise-test "${SRCROOT}/nrlmsise-00_test.c")
        target_link_libraries(nrlmsise-test nrlmsise00 m)
        add_test(nrlmsise-test "${SRCROOT}/bin/nrlmsise-test")
ENDIF()

# End of file.
