cmake_minimum_required(VERSION 3.19)

project(ncdiag
        VERSION 1.1.2
        LANGUAGES Fortran)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

include(${PROJECT_NAME}_compiler_flags)
include(GNUInstallDirs)

if(NOT CMAKE_BUILD_TYPE MATCHES "^(Debug|Release|RelWithDebInfo|MinSizeRel)$")
  message(STATUS "Setting build type to 'Release' as none was specified.")
  set(CMAKE_BUILD_TYPE
      "Release"
      CACHE STRING "Choose the type of build." FORCE)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

if(NOT CMAKE_Fortran_COMPILER_ID MATCHES "^(GNU|Intel)$")
  message(WARNING "${CMAKE_Fortran_COMPILER_ID} is not supported.")
endif()

option(ENABLE_NCDIAG_SERIAL "Enable Serial NetCDF diagnostics" ON)

# Dependencies
find_package(MPI REQUIRED)
find_package(NetCDF REQUIRED COMPONENTS Fortran)

# Echo user options
message(STATUS "ENABLE_NCDIAG_SERIAL ... ${ENABLE_NCDIAG_SERIAL}")

add_subdirectory(ncdiag)
