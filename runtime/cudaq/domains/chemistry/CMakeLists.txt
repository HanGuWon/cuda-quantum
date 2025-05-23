# ============================================================================ #
# Copyright (c) 2022 - 2025 NVIDIA Corporation & Affiliates.                   #
# All rights reserved.                                                         #
#                                                                              #
# This source code and the accompanying materials are made available under     #
# the terms of the Apache License 2.0 which accompanies this distribution.     #
# ============================================================================ #

add_library(cudaq-chemistry SHARED molecule.cpp)

set (CHEMISTRY_DEPENDENCIES "")
list(APPEND CHEMISTRY_DEPENDENCIES cudaq-operator)
add_openmp_configurations(cudaq-chemistry CHEMISTRY_DEPENDENCIES)

target_link_libraries(cudaq-chemistry PRIVATE ${CHEMISTRY_DEPENDENCIES})
target_include_directories(cudaq-chemistry SYSTEM
   PRIVATE 
     ${CMAKE_SOURCE_DIR}/tpls/xtl/include 
     ${CMAKE_SOURCE_DIR}/tpls/xtensor/include)

# Bug in GCC 12 leads to spurious warnings (-Wrestrict)
# https://gcc.gnu.org/bugzilla/show_bug.cgi?id=105329
if (CMAKE_COMPILER_IS_GNUCXX 
  AND CMAKE_CXX_COMPILER_VERSION VERSION_GREATER_EQUAL 12.0.0 
  AND CMAKE_CXX_COMPILER_VERSION VERSION_LESS 13.0.0)
  target_compile_options(cudaq-chemistry PRIVATE --param=evrp-mode=legacy)
endif()

install(TARGETS cudaq-chemistry DESTINATION lib)