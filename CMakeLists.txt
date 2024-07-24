cmake_minimum_required(VERSION 2.8.3)
project(gnss_lidar_calib)

add_definitions(-msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2)
set(CMAKE_CXX_FLAGS "-msse -msse2 -msse3 -msse4 -msse4.1 -msse4.2")
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_LIST_DIR}/cmake")

set(CMAKE_BUILD_TYPE "RELEASE")
#SET(CMAKE_C_FLAGS_DEBUG "-D_DEBUG")

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")  # Adjust the path as needed
find_package(JsonCpp REQUIRED)
find_package(PkgConfig REQUIRED)
pkg_check_modules(JSONCPP REQUIRED jsoncpp)

find_package(catkin REQUIRED COMPONENTS
  geometry_msgs
  novatel_oem7_msgs
  roscpp
  sensor_msgs
  std_msgs
  nav_msgs
  visualization_msgs
  tf2
  rosbag
  ndt_omp
  fast_gicp
  pcl_ros
)

find_package(Ceres REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED)
find_package(CURL REQUIRED)
include_directories(${PCL_INCLUDE_DIRS})
link_directories(${PCL_LIBRARY_DIRS})
add_definitions(${PCL_DEFINITIONS})
link_directories(${JSONCPP_LIBRARY_DIRS})

message(STATUS "PCL_INCLUDE_DIRS:" ${PCL_INCLUDE_DIRS})
message(STATUS "PCL_LIBRARY_DIRS:" ${PCL_LIBRARY_DIRS})
message(STATUS "PCL_DEFINITIONS:" ${PCL_DEFINITIONS})

catkin_package(
  INCLUDE_DIRS include
#  LIBRARIES ${PROJECT_NAME}
   CATKIN_DEPENDS
   roscpp
   sensor_msgs
   geometry_msgs
   std_msgs
   nav_msgs
   visualization_msgs
   rosbag
#  DEPENDS system_lib
)

include_directories(include)
include_directories(
  ${catkin_INCLUDE_DIRS}
  ${PCL_INCLUDE_DIRS}
  ${Eigen3_INCLUDE_DIRS}
  ${CERES_INCLUDE_DIRS}
  ${CURL_INCLUDE_DIRS}
  ${JSONCPP_INCLUDE_DIRS}
)

SET(CMAKE_CXX_FLAGS "-O3 -g -Wall ${CMAKE_CXX_FLAGS}")

add_executable(gnss_lidar_calib src/gnss_lidar_calib/main.cpp
        src/gnss_lidar_calib/utilities.cpp
        src/gnss_lidar_calib/cloud_preprocessor.cpp
        src/gnss_lidar_calib/deskew.cpp
        src/gnss_lidar_calib/data_reader.cpp
        src/gnss_lidar_calib/data_synchronizer.cpp
        src/gnss_lidar_calib/lidar_odometry_generator.cpp
        src/gnss_lidar_calib/calibrator.cpp
        src/gnss_lidar_calib/outlier_detection.cpp
        src/gnss_lidar_calib/elevation_correction.cpp)
target_link_libraries(gnss_lidar_calib ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${Eigen3_INCLUDE_DIRS} ${CERES_LIBRARIES} ${JSONCPP_LIBRARIES} ${CURL_LIBRARIES})

install(
  DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)

install(
    DIRECTORY rviz launch
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)