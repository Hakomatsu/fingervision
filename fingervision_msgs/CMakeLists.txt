cmake_minimum_required(VERSION 3.0.2)
project(fingervision_msgs)

add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  message_generation
  std_msgs
  )

add_message_files(
  FILES
  Filter1ObjInfo.msg
  Filter1Wrench.msg
  BlobMove.msg
  Int32Array.msg
  BlobMoves.msg
  ProxVision.msg
  )

add_service_files(
  FILES
  SetInt32.srv
  SetString.srv
  TakeSnapshot.srv
  )

generate_messages(
  DEPENDENCIES std_msgs
)

catkin_package(
  CATKIN_DEPENDS message_runtime
  )

include_directories(
  ${catkin_INCLUDE_DIRS}
  )
