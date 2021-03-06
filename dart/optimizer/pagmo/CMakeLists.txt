# Dependency checks
dart_find_package(pagmo)
dart_check_optional_package(pagmo "dart-optimizer-pagmo" "pagmo" "2.8")
if(pagmo_FOUND)
  set(HAVE_PAGMO TRUE CACHE BOOL "Check if pagmo found." FORCE)
else()
  set(HAVE_PAGMO FALSE CACHE BOOL "Check if pagmo found." FORCE)
endif()

find_package(Threads REQUIRED)
dart_check_optional_package(Threads "dart-optimizer-pagmo" "Threads" "")

# Search all header and source files
file(GLOB hdrs "*.hpp")
file(GLOB srcs "*.cpp")

# Set local target name
set(target_name ${PROJECT_NAME}-optimizer-pagmo)
set(component_name optimizer-pagmo)

# Add target
dart_add_library(${target_name} ${hdrs} ${srcs})
target_link_libraries(${target_name} PUBLIC dart Pagmo::pagmo)

# Thread
if(THREADS_HAVE_PTHREAD_ARG)
  target_compile_options(${target_name} PUBLIC "-pthread")
endif()
if(CMAKE_THREAD_LIBS_INIT)
  target_link_libraries(${target_name} PUBLIC ${CMAKE_THREAD_LIBS_INIT})
endif()

# Component
add_component(${PROJECT_NAME} ${component_name})
add_component_targets(${PROJECT_NAME} ${component_name} ${target_name})
add_component_dependencies(${PROJECT_NAME} ${component_name} dart)
add_component_dependency_packages(${PROJECT_NAME} ${component_name} pagmo NLOPT)

# Generate header for this namespace
dart_get_filename_components(header_names "optimizer_pagmo headers" ${hdrs})
dart_generate_include_header_file(
  "${CMAKE_CURRENT_BINARY_DIR}/pagmo.hpp"
  "dart/optimizer/pagmo/"
  ${header_names}
)

# Install
install(
  FILES ${hdrs} ${CMAKE_CURRENT_BINARY_DIR}/pagmo.hpp
  DESTINATION include/dart/optimizer/pagmo
  COMPONENT headers
)

dart_format_add(${hdrs} ${srcs})
