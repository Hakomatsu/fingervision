cmake_minimum_required(VERSION 3.0.2)
project(fingervision)

add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
  std_msgs
  genmsg
  fingervision_msgs
  cv_bridge
  image_transport
  camera_info_manager
)

set(PROJECT_BASE_DIR ${PROJECT_SOURCE_DIR}/..)
set(FV_CORE_DIR ${PROJECT_BASE_DIR}/fv_core)
set(THIRDPARTY_DIR ${PROJECT_BASE_DIR}/3rdparty)
include_directories(${FV_CORE_DIR})
include_directories(${THIRDPARTY_DIR})

# ++++OPENCV-3.x++++
find_package(OpenCV 4 REQUIRED)
message("OpenCV_INCLUDE_DIRS: ${OpenCV_INCLUDE_DIRS}")
message("OpenCV_LIBRARIES: ${OpenCV_LIBRARIES}")
# ++++OPENCV-2.x++++
# find_package(OpenCV 2 REQUIRED)
# message("OpenCV_INCLUDE_DIRS: ${OpenCV_INCLUDE_DIRS}")
# message("OpenCV_LIBRARIES: ${OpenCV_LIBRARIES}")
# ++++OPENCV-2.4.13++++
# # NOTE: Install OpenCV 2.4.13 on ~/.local from source which includes cv::fisheye
# # We don't use opencv found by any prior running of find_package
# unset(OpenCV_CONFIG_PATH CACHE)
# unset(OpenCV_DIR CACHE)
# set(TMP_PREFIX_PATH ${CMAKE_PREFIX_PATH})
# set(CMAKE_PREFIX_PATH "$ENV{HOME}/.local")
# find_package(OpenCV 2.4.13 REQUIRED)
# # restore CMAKE_PREFIX_PATH and other cached variables
# # in order to avoid other packages find this opencv.
# set(CMAKE_PREFIX_PATH ${TMP_PREFIX_PATH})
# unset(OpenCV_CONFIG_PATH CACHE)
# unset(OpenCV_DIR CACHE)
# message("OpenCV_INCLUDE_DIRS: ${OpenCV_INCLUDE_DIRS}")
# message("OpenCV_LIBRARIES: ${OpenCV_LIBRARIES}")
# ----OPENCV----

find_package(Eigen3 REQUIRED)
include_directories(${EIGEN3_INCLUDE_DIR})

catkin_package(
 INCLUDE_DIRS ../3rdparty
 CATKIN_DEPENDS roscpp rospy std_msgs
 )
include_directories(
  ${catkin_INCLUDE_DIRS}
  )

add_library(fv_blob_tracker2
  ${FV_CORE_DIR}/blob_tracker2.cpp
  )
target_link_libraries(fv_blob_tracker2
  ${OpenCV_LIBRARIES}
  ${catkin_LIBRARIES}
  )
add_library(fv_prox_vision
  ${FV_CORE_DIR}/prox_vision.cpp
  )
target_link_libraries(fv_prox_vision
  ${OpenCV_LIBRARIES}
  ${catkin_LIBRARIES}
  )
# WITH_STEREO:
# rosbuild_add_library(ayv_usb_stereo
#       ${THIRDPARTY_DIR}/ay_vision/usb_stereo.cpp
#       )
# target_link_libraries(ayv_usb_stereo
#       ${OpenCV_LIBRARIES}
#       )
add_library(ay_vision
  ${THIRDPARTY_DIR}/ay_vision/vision_util.cpp
  )
target_link_libraries(ay_vision
  ${OpenCV_LIBRARIES}
  ${catkin_LIBRARIES}
  )

#Building executables:

# NOTE: Adding libcv_bridge.so is a quick hack.
# In manifest.xml cv_bridge is omitted.
# If we put cv_bridge as a dependency in manifest.xml,
# both opencv 2.4.8 and 2.4.13 will be linked simultaneously,
# which causes a compile issue.

add_executable(fv_core_node
  src/fv_core_node.cpp
  )
target_link_libraries(fv_core_node
  ${catkin_LIBRARIES}
  fv_blob_tracker2
  fv_prox_vision
  ay_vision
  ${OpenCV_LIBRARIES}
  boost_thread
  boost_system
  # ${PROJECT_SOURCE_DIR}/lib/libcv_bridge.so
  )
# add_dependencies(fv_core_node fingervision_msgs_generate_message_cpp)
# add_dependencies(fv_core_node fingervision_msgs_gencpp)

