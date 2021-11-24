cmake_minimum_required(VERSION 3.5)
project(serial_utils)
include(FetchContent)
include(CMakePrintHelpers)
include(cmake/CPM.cmake)

CPMAddPackage(
  NAME serial
  GIT_REPOSITORY https://github.com/andyblarblar/serial-ros2
  GIT_TAG master
)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(Boost 1.71 CONFIG REQUIRED COMPONENTS thread)

add_library(${PROJECT_NAME}
    src/serial_listener.cc
    include/serial/utils/serial_listener.h
    include/serial/utils/concurrent_queue.h
)

## Sources
## Add serial library

ament_target_dependencies(
  serial_utils
  Boost
  rclcpp
)

target_link_libraries(serial_utils Boost::thread serial)

target_include_directories(serial_utils
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
    )

## Install executable
install(TARGETS ${PROJECT_NAME}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

## Install headers
install(FILES include/serial/utils/serial_listener.h include/serial/utils/concurrent_queue.h
    DESTINATION include/serial/utils
)

ament_export_libraries(${PROJECT_NAME})
ament_export_include_directories(include)

## Tests
#if(BUILD_TESTING)
#    add_subdirectory(tests)
#endif()

ament_package()
