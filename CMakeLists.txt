cmake_minimum_required(VERSION 2.8.3)

project(camera_aravis)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/CMakeModules)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  nodelet
  std_msgs
  sensor_msgs
  message_generation
  image_transport
  camera_info_manager
  dynamic_reconfigure
  tf
  tf2_ros
)
find_package(Aravis REQUIRED)
find_package(GLIB2 REQUIRED)

generate_dynamic_reconfigure_options(cfg/CameraAravisConfig.cfg)

add_message_files(
   FILES
   CameraAutoInfo.msg
)

generate_messages(
   DEPENDENCIES
   std_msgs
)

catkin_package(
    DEPENDS Aravis GLIB2
    CATKIN_DEPENDS roscpp nodelet std_msgs sensor_msgs message_runtime image_transport camera_info_manager dynamic_reconfigure tf tf2_ros
    INCLUDE_DIRS
    LIBRARIES
)

include_directories(cfg
  ${catkin_INCLUDE_DIRS}
  ${Aravis_INCLUDE_DIRS}
  ${GLIB2_INCLUDE_DIRS})

set(LIBRARIES
  ${catkin_LIBRARIES}
  ${Aravis_LIBRARIES}
  glib-2.0
  gmodule-2.0
  gobject-2.0
)

add_library(${PROJECT_NAME}
  src/camera_aravis_nodelet.cpp
  src/camera_buffer_pool.cpp
)
target_link_libraries(${PROJECT_NAME} ${LIBRARIES})
add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

add_executable(cam_aravis
  src/camera_aravis_node.cpp
)
target_link_libraries(cam_aravis ${PROJECT_NAME} ${LIBRARIES})
add_dependencies(cam_aravis ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)

install(TARGETS ${PROJECT_NAME}
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_GLOBAL_BIN_DESTINATION}
)

install(TARGETS cam_aravis
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY launch/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch
  FILES_MATCHING PATTERN "*.launch"
  PATTERN ".svn" EXCLUDE
)

install(FILES
  nodelet_plugins.xml
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
