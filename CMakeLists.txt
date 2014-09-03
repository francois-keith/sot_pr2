cmake_minimum_required(VERSION 2.4.6)

project(sot_pr2)

find_package(catkin REQUIRED COMPONENTS roscpp dynamic_graph_bridge_msgs dynamic_graph_bridge pr2_controller_interface pr2_mechanism_model actionlib control_toolbox) 
find_package(Boost REQUIRED COMPONENTS program_options)
find_package(orocos_kdl REQUIRED)

catkin_package(
  CATKIN_DEPENDS dynamic_graph_bridge_msgs dynamic_graph_bridge
)


include(cmake/base.cmake)
include(cmake/python.cmake)
if(NOT DEFINED CMAKE_INSTALL_LIBDIR)
  set(CMAKE_INSTALL_LIBDIR lib)
endif()
include(cmake/GNUInstallDirs.cmake)

# Search for dependencies.
ADD_REQUIRED_DEPENDENCY("jrl-mathtools")
ADD_REQUIRED_DEPENDENCY("jrl-mal")
ADD_REQUIRED_DEPENDENCY("dynamic-graph")
ADD_REQUIRED_DEPENDENCY("dynamic-graph-python")
ADD_REQUIRED_DEPENDENCY("sot-core")
ADD_REQUIRED_DEPENDENCY("sot-application >= 1.0.0")
ADD_REQUIRED_DEPENDENCY("jrl-dynamics-urdf >= 2.0.1-14")

# This is required by the Python prologue.
ADD_REQUIRED_DEPENDENCY("sot-dynamic")


# Handle rpath necessary to handle ROS multiplace packages
# libraries inclusion
SET(CMAKE_SKIP_BUILD_RPATH  FALSE)
SET(CMAKE_BUILD_WITH_INSTALL_RPATH FALSE)
SET(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_LIBDIR}")
SET(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)

# Python
INCLUDE(cmake/python.cmake)
FINDPYTHON()
include_directories(include ${catkin_INCLUDE_DIRS})
INCLUDE_DIRECTORIES(${PYTHON_INCLUDE_DIRS})
LINK_DIRECTORIES(${PYTHON_LIBRARY_DIRS})

set(SOURCES
    src/pr2_controller_plugin.cpp
    src/pr2_threaded_sot_controller.cpp
    src/pr2_sot_controller.cpp
    src/pr2_device.cpp)

set(HEADERS
    include/sot_pr2/pr2_controller_plugin.h
    include/sot_pr2/pr2_threaded_sot_controller.h
    include/sot_pr2/pr2_sot_controller.h
    include/sot_pr2/pr2_device.h)

add_library(sot_pr2 ${HEADERS} ${SOURCES})

PKG_CONFIG_USE_DEPENDENCY(sot_pr2 "dynamic-graph")
PKG_CONFIG_USE_DEPENDENCY(sot_pr2 "sot-core")
PKG_CONFIG_USE_DEPENDENCY(sot_pr2 "dynamic-graph-python")
PKG_CONFIG_USE_DEPENDENCY(sot_pr2 "jrl-dynamics-urdf")
target_link_libraries(sot_pr2 orocos-kdl)
target_link_libraries(sot_pr2 ${catkin_LIBRARIES})
install(TARGETS sot_pr2 DESTINATION ${CMAKE_INSTALL_LIBDIR})

add_executable(sot_pr2_fake_controller ${HEADERS} src/fake_controller.cpp)
PKG_CONFIG_USE_DEPENDENCY(sot_pr2_fake_controller "dynamic-graph")
PKG_CONFIG_USE_DEPENDENCY(sot_pr2_fake_controller "sot-core")
PKG_CONFIG_USE_DEPENDENCY(sot_pr2_fake_controller "dynamic-graph-python")
PKG_CONFIG_USE_DEPENDENCY(sot_pr2_fake_controller "jrl-dynamics-urdf")
target_link_libraries(sot_pr2_fake_controller sot_pr2 orocos-kdl)
target_link_libraries(sot_pr2_fake_controller ${catkin_LIBRARIES})
install(TARGETS sot_pr2_fake_controller DESTINATION ${CMAKE_INSTALL_LIBDIR})

ADD_SUBDIRECTORY(src)
ADD_SUBDIRECTORY(python)

