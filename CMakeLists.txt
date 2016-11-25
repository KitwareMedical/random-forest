cmake_minimum_required(VERSION 2.6)

project( RandomForest )

include_directories(include)

file(GLOB headers include/andres/*.hxx include/andres/ml/*.hxx)

enable_testing()

##############################################################################
# Doxygen
##############################################################################
find_package(Doxygen QUIET)
if(DOXYGEN_FOUND)
  message(STATUS "Doxygen found")
else()
  message("doxygen not found")
endif()

##############################################################################
# C++11 support
##############################################################################
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
if(COMPILER_SUPPORTS_CXX11)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif(COMPILER_SUPPORTS_CXX0X)
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
else()
  message(STATUS
    "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Some functionality will not be available.")
endif()

##############################################################################
# MSVC-specific settings
##############################################################################
if(MSVC)
  add_definitions(-D_CRT_SECURE_NO_WARNINGS)
endif()

##############################################################################
# OpenMP
##############################################################################
find_package(OpenMP)
if(OPENMP_FOUND)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  message(STATUS "OpenMP found")
else()
  message("OpenMP not found")
endif()

##############################################################################
# targets
##############################################################################
set( EXECUTABLE_OUTPUT_PATH "${RandomForest_BINARY_DIR}/bin" )
set( LIBRARY_OUTPUT_PATH "${RandomForest_BINARY_DIR}/lib" )

add_executable( test-decision-trees src/unittest/ml/test-decision-trees.cxx
  ${headers} )
add_test( test-decision-trees test-decision-trees )

if(DOXYGEN_FOUND)
  configure_file(
    "${RandomForest_SOURCE_DIR}/doxygen/doxyfile-ml-decision-trees.in"
    "${RandomForest_BINARY_DIR}/doxyfile-ml-decision-trees" @ONLY IMMEDIATE )
  add_custom_target(doc-ml-decision-trees ALL COMMAND ${DOXYGEN}
    "${RandomForest_BINARY_DIR}/doxyfile-ml-decision-trees" )
endif()

install( FILES include/andres/marray.hxx DESTINATION include/andres )
install( FILES include/andres/ml/decision-trees.hxx DESTINATION
 include/andres/ml )
