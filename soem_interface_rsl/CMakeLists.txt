cmake_minimum_required(VERSION 3.15)
project(soem_interface_rsl)

add_definitions(-std=c++17 -Wall -Werror)
add_definitions(-DMELO_USE_COUT)

# 改为 catkin 方式
find_package(catkin REQUIRED COMPONENTS
  message_logger
  soem_rsl
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES ${PROJECT_NAME}
  CATKIN_DEPENDS message_logger soem_rsl
)

# 保持原有 add_library 不变
add_library(
  ${PROJECT_NAME} SHARED
  src/${PROJECT_NAME}/common/ThreadSleep.cpp
  src/${PROJECT_NAME}/common/Macros.cpp
  src/${PROJECT_NAME}/EthercatSlaveBase.cpp
  src/${PROJECT_NAME}/EthercatBusManagerBase.cpp
  src/${PROJECT_NAME}/EthercatBusBase.cpp
)

target_link_options(${PROJECT_NAME} PRIVATE "-Wl,--exclude-libs,ALL")

target_include_directories(${PROJECT_NAME}
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
    ${catkin_INCLUDE_DIRS}
)

# 移除 ament_target_dependencies，改用 target_link_libraries
target_link_libraries(${PROJECT_NAME}
  ${catkin_LIBRARIES}
)

# Set output directory to devel/lib for catkin workspace integration
set_target_properties(${PROJECT_NAME} PROPERTIES
  ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/../../devel/lib
  LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/../../devel/lib
  RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/../../devel/bin
)

install(
    DIRECTORY include/
    DESTINATION include/${PROJECT_NAME}
)

install(
    TARGETS ${PROJECT_NAME}
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)