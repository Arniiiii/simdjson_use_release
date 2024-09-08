cmake_minimum_required(VERSION 3.14...3.22)

# ---- Project ----

# Note: update this to your new project's name and version
project(
  simdjson_use_release
  VERSION ${simdjson_VERSION}
  LANGUAGES CXX)

option(BUILD_SHARED_LIBS "yes/no" NO)

if(${BUILD_SHARED_LIBS})
  # https://stackoverflow.com/questions/33062728/cmake-link-shared-library-on-windows
  if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS TRUE)
  endif()
endif()

set(FETCHCONTENT_QUIET
    OFF
    CACHE BOOL "Make downloading of packages quiet")

# ---- Include guards ----

if(PROJECT_SOURCE_DIR STREQUAL PROJECT_BINARY_DIR)
  message(
    FATAL_ERROR
      "In-source builds not allowed. Please make a new directory (called a build directory) and run CMake from there."
  )
endif()

include(getCPM.cmake)

set(packageProject_VERSION 1.12.0)
cpmaddpackage(
  NAME
  PackageProject.cmake
  VERSION
  ${packageProject_VERSION}
  URL
  "https://github.com/TheLartians/PackageProject.cmake/archive/refs/tags/v${packageProject_VERSION}.zip"
)

file(
  DOWNLOAD
  https://github.com/simdjson/simdjson/releases/download/v${simdjson_VERSION}/simdjson.cpp
  ./simdjson/simdjson.cpp)
file(
  DOWNLOAD
  https://github.com/simdjson/simdjson/releases/download/v${simdjson_VERSION}/simdjson.h
  ./simdjson/include/simdjson.h)

add_library(simdjson ./simdjson/simdjson.cpp)
target_include_directories(
  simdjson
  PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/simdjson/include>
         $<INSTALL_INTERFACE:include/simdjson>)

# the location where the project's version header will be placed should match
# the project's regular header paths
string(TOLOWER simdjson/version.h
               simdjson_VERSION_HEADER_LOCATION)
string(TOLOWER simdjson/export.h simdjson_EXPORT_HEADER_LOCATION)

set_property(TARGET simdjson PROPERTY VERSION ${simdjson_VERSION})
set_property(TARGET simdjson PROPERTY SOVERSION ${simdjson_VERSION})

packageproject(
  NAME
  simdjson
  VERSION
  ${simdjson_VERSION}
  NAMESPACE
  simdjson
  BINARY_DIR
  ${PROJECT_BINARY_DIR}
  INCLUDE_DIR
  ${PROJECT_SOURCE_DIR}/simdjson/include
  INCLUDE_DESTINATION
  include
  VERSION_HEADER
  "${simdjson_VERSION_HEADER_LOCATION}"
  EXPORT_HEADER
  "${simdjson_EXPORT_HEADER_LOCATION}"
  COMPATIBILITY
  "AnyNewerVersion"
  DISABLE_VERSION_SUFFIX
  ON)
