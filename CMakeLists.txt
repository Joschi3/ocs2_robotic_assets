cmake_minimum_required(VERSION 3.10)
project(ocs2_robotic_assets)

find_package(ament_cmake REQUIRED)

# --- Generate a header with the package path (builddir -> install) ---
# Prefer generating into the build tree (don’t write into source).
set(GENERATED_INCLUDE_DIR ${CMAKE_CURRENT_BINARY_DIR}/include/${PROJECT_NAME})
file(MAKE_DIRECTORY ${GENERATED_INCLUDE_DIR})

# Expose a variable the template can substitute.
set(PACKAGE_SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

configure_file(
  ${CMAKE_CURRENT_SOURCE_DIR}/include/${PROJECT_NAME}/package_path.h.in
  ${GENERATED_INCLUDE_DIR}/package_path.h
  @ONLY
)

# Header-only target so downstream packages can link against ocs2_robotic_assets::ocs2_robotic_assets
add_library(${PROJECT_NAME} INTERFACE)
target_include_directories(${PROJECT_NAME} INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

# ------------
# Installation
# ------------
install(TARGETS ${PROJECT_NAME} EXPORT export_${PROJECT_NAME})

# Install all static headers, but NOT the .in template.
install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION include/${PROJECT_NAME}
  FILES_MATCHING
    PATTERN "*.h" PATTERN "*.hpp"
    PATTERN "package_path.h.in" EXCLUDE
)

# Install the generated header.
install(
  FILES ${GENERATED_INCLUDE_DIR}/package_path.h
  DESTINATION include/${PROJECT_NAME}
)

# Install data files.
install(
  DIRECTORY resources
  DESTINATION share/${PROJECT_NAME}
)

# Export includes for downstream users.
ament_export_include_directories(include)
ament_export_targets(export_${PROJECT_NAME})

ament_package()
