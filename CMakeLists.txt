cmake_minimum_required(VERSION 2.8.3)
project(gphoto2_ros)

find_package(catkin REQUIRED COMPONENTS
  message_generation
  roscpp
  self_test
  sensor_msgs
  std_msgs
  std_srvs
)

find_package(OpenCV REQUIRED)

find_package(PkgConfig REQUIRED)
pkg_check_modules(gphoto2 REQUIRED libgphoto2)

add_service_files(FILES
  Capture.srv
  GetConfig.srv
  SetConfig.srv
  DownloadPictures.srv
)

generate_messages(DEPENDENCIES
  std_msgs
  sensor_msgs
)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES gphoto2_ros
  CATKIN_DEPENDS roscpp std_msgs sensor_msgs self_test
  DEPENDS OpenCV gphoto2
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
  ${gphoto2_INCLUDE_DIRS}
)

add_library(gphoto2_ros
   src/libphoto2/photo_reporter.cpp
   src/libphoto2/photo_camera_list.cpp
   src/libphoto2/photo_camera.cpp
   src/libphoto2/photo_image.cpp
)

add_executable(photo_node src/photo_node/photo_node.cpp)
add_executable(test_photo src/test_photo/test_photo.cpp)

add_dependencies(photo_node photo_generate_messages_cpp)
add_dependencies(test_photo photo_generate_messages_cpp)

target_link_libraries(gphoto2_ros
  ${OpenCV_LIBRARIES}
)  

target_link_libraries(photo_node
  gphoto2_ros
  ${catkin_LIBRARIES}
  ${gphoto2_LIBRARIES}
)

target_link_libraries(test_photo
  gphoto2_ros
  ${catkin_LIBRARIES}
  ${OpenCV_LIBRARIES}
)

install(TARGETS gphoto2_ros photo_node test_photo
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
