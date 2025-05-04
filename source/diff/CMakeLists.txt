# Copyright (c) 2022 Google LLC.

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
set(SPIRV_TOOLS_DIFF_SOURCES
  diff.h
  lcs.h

  diff.cpp
)

add_library(diff ${SPIRV_TOOLS_LIBRARY_TYPE} ${SPIRV_TOOLS_DIFF_SOURCES})

spvtools_default_compile_options(diff)
target_include_directories(diff
  PUBLIC
	$<BUILD_INTERFACE:${spirv-tools_SOURCE_DIR}/include>
	$<BUILD_INTERFACE:${SPIRV_HEADER_INCLUDE_DIR}>
	$<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
  PRIVATE ${spirv-tools_BINARY_DIR}
)
# We need the assembling and disassembling functionalities in the main library.
target_link_libraries(diff
  PUBLIC ${SPIRV_TOOLS_FULL_VISIBILITY})
# We need the internals of spirv-opt.
target_link_libraries(diff
  PUBLIC opt)

set_property(TARGET diff PROPERTY FOLDER "${SPIRV_TOOLS} libraries")
spvtools_check_symbol_exports(diff)

if(ENABLE_SPIRV_TOOLS_INSTALL)
  install(TARGETS diff EXPORT diffTargets)
  export(EXPORT diffTargets FILE diffTargets.cmake)

  spvtools_config_package_dir(diff PACKAGE_DIR)
  install(EXPORT diffTargets FILE diffTargets.cmake
  	DESTINATION ${PACKAGE_DIR})

  spvtools_generate_config_file(diff)
  install(FILES ${CMAKE_BINARY_DIR}/diffConfig.cmake DESTINATION ${PACKAGE_DIR})
endif(ENABLE_SPIRV_TOOLS_INSTALL)
