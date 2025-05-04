# Copyright (c) 2021 Google LLC.

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
set(SPIRV_TOOLS_LINT_SOURCES
  divergence_analysis.h
  lints.h

  linter.cpp
  divergence_analysis.cpp
  lint_divergent_derivatives.cpp
)

if(MSVC AND (NOT ("${CMAKE_CXX_COMPILER_ID}" MATCHES "Clang")))
  # Enable parallel builds across four cores for this lib.
  add_definitions(/MP4)
endif()

add_library(lint ${SPIRV_TOOLS_LIBRARY_TYPE} ${SPIRV_TOOLS_LINT_SOURCES})

spvtools_default_compile_options(lint)
target_include_directories(lint
  PUBLIC
	$<BUILD_INTERFACE:${spirv-tools_SOURCE_DIR}/include>
	$<BUILD_INTERFACE:${SPIRV_HEADER_INCLUDE_DIR}>
	$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  PRIVATE ${spirv-tools_BINARY_DIR}
)
# We need the assembling and disassembling functionalities in the main library.
target_link_libraries(lint
  PUBLIC ${SPIRV_TOOLS_FULL_VISIBILITY})
# We need the internals of spirv-opt.
target_link_libraries(lint
  PUBLIC opt)

set_property(TARGET lint PROPERTY FOLDER "SPIRV-Tools libraries")
spvtools_check_symbol_exports(lint)

if(ENABLE_SPIRV_TOOLS_INSTALL)
  install(TARGETS lint EXPORT lintTargets)
  export(EXPORT lintTargets FILE lintTargets.cmake)

  spvtools_config_package_dir(lint PACKAGE_DIR)
  install(EXPORT lintTargets FILE lintTargets.cmake
  	DESTINATION ${PACKAGE_DIR})

  spvtools_generate_config_file(lint)
  install(FILES ${CMAKE_BINARY_DIR}/lintConfig.cmake DESTINATION ${PACKAGE_DIR})
endif(ENABLE_SPIRV_TOOLS_INSTALL)
