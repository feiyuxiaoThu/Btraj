cmake_minimum_required(VERSION 3.13)
project(bezier_planer)

add_subdirectory(${PROJECT_SOURCE_DIR}/third_party/sdf_tools)

find_package(catkin REQUIRED COMPONENTS 
    roscpp 
    std_msgs
    nav_msgs
    visualization_msgs 
    quadrotor_msgs
    tf
)

find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED)

find_package(osqp REQUIRED)
get_target_property(osqp_INCLUDE_DIR osqp::osqpstatic INTERFACE_INCLUDE_DIRECTORIES)
include_directories(${osqp_INCLUDE_DIR})

set(Eigen3_INCLUDE_DIRS ${EIGEN3_INCLUDE_DIR})

catkin_package(
  INCLUDE_DIRS include
)

# Note you should install osqp first
#link_directories( ${PROJECT_SOURCE_DIR}/third_party/mosek/lib/mosek8_1 )
#link_directories(${PROJECT_SOURCE_DIR}/third_party/osqp/lib)

include_directories(${PROJECT_SOURCE_DIR}/include)
#include_directories(${PROJECT_SOURCE_DIR}/third_party/mosek/include)
#include_directories(${PROJECT_SOURCE_DIR}/third_party/osqp/include)
include_directories(${PROJECT_SOURCE_DIR}/third_party/sdf_tools/include)

include_directories(
    include 
    SYSTEM 
    third_party
    ${catkin_INCLUDE_DIRS} 
    ${Eigen3_INCLUDE_DIRS} 
    ${PCL_INCLUDE_DIRS}
)

set(CMAKE_CXX_FLAGS "-std=c++0x ${CMAKE_CXX_FLAGS} -O3 -Wall") # -Wextra -Werror

add_executable(
    b_traj_node 
    src/b_traj_node.cpp
    src/bezier_base.cpp
    src/trajectory_generator.cpp
    src/a_star.cpp
    third_party/fast_methods/console/console.cpp
    third_party/fast_methods/fm/fmdata/fmcell.cpp
    third_party/fast_methods/ndgridmap/cell.cpp
)
target_link_libraries(b_traj_node ${catkin_LIBRARIES} sdf_tools ${PCL_LIBRARIES} osqp::osqp)

add_executable ( b_traj_server src/traj_server.cpp src/bezier_base.cpp)
target_link_libraries( b_traj_server
                        ${catkin_LIBRARIES}
)

add_executable ( random_forest_sensing src/random_forest_sensing.cpp)
target_link_libraries( random_forest_sensing
                        ${catkin_LIBRARIES}
                        ${PCL_LIBRARIES}
)

add_executable ( odom_generator src/odom_generator.cpp )
target_link_libraries( odom_generator
                        ${catkin_LIBRARIES}
)
