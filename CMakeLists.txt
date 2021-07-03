cmake_minimum_required(VERSION 2.8.3)
project(livox_laser_simulation)

## Compile as C++11, supported in ROS Kinetic and newer
#add_compile_options(-std=c++11)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  tf
)

find_package(gazebo REQUIRED)
find_package(PCL REQUIRED)

find_package(PCL)
if(PCL_FOUND)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${PCL_C_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${PCL_CXX_FLAGS}")
endif()

find_package(ODE)
if(ODE_FOUND)
  set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${ODE_C_FLAGS}")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${ODE_CXX_FLAGS}")
endif()


find_package(catkin REQUIRED COMPONENTS
  pcl_conversions
  tf
  pcl_ros
  gazebo_ros
)

catkin_package(  
  INCLUDE_DIRS include
  LIBRARIES 
  livox_laser_simulation
  )

include_directories(  
  include
  ${catkin_INCLUDE_DIRS} 
  ${GAZEBO_INCLUDE_DIRS}
)

link_directories(
  ${GAZEBO_LIBRARY_DIRS}
)

SET(CMAKE_CXX_FLAGS "-O2 -g -Wall ${CMAKE_CXX_FLAGS}")

catkin_package(
  CATKIN_DEPENDS gazebo_ros
  DEPENDS PCL
)

#This is designed for whose proto installed in system is not 2.6. We can install the version of proto in local dir
#include_directories(/home/lfc/proto/include/)
#link_directories(/home/lfc/proto/lib/)

#add_executable(livox_laser_simulation src/livox_points_plugin.cpp src/livox_ode_multiray_shape.cpp)

set(SOURCE_FILES
	src/livox_points_plugin.cpp
	src/livox_ode_multiray_shape.cpp
)

add_library(livox_laser_simulation ${SOURCE_FILES})
add_dependencies(livox_laser_simulation ${catkin_EXPORTED_TARGETS})
target_link_libraries(livox_laser_simulation ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES} RayPlugin)

#target_link_libraries(livox_laser_simulation ${catkin_LIBRARIES} RayPlugin)

add_dependencies(livox_laser_simulation
  ${catkin_EXPORTED_TARGETS}
)
target_link_libraries(livox_laser_simulation ${catkin_LIBRARIES})

#install(DIRECTORY include/${PROJECT_NAME}/
#  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#  FILES_MATCHING PATTERN "*.h"
#)

install(
  TARGETS 
    livox_laser_simulation
  DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
)

foreach(dir launch urdf meshes rviz scan_mode resources worlds)
  install(DIRECTORY ${dir}/
    DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir}
  )
endforeach(dir)
