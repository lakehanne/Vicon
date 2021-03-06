cmake_minimum_required(VERSION 2.8.3)

project(vicon_listener)

OPTION(ENABLE_CXX11 "Enable C++11 support" ON)

# Check for c++11 support
INCLUDE(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
CHECK_CXX_COMPILER_FLAG("-std=c++0x" COMPILER_SUPPORTS_CXX0X)
IF(COMPILER_SUPPORTS_CXX11)
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
ELSEIF(COMPILER_SUPPORTS_CXX0X)
SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++0x")
ELSE()
  MESSAGE(ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
ENDIF()

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  diagnostic_updater
  dynamic_reconfigure
  geometry_msgs
#  message_generation
#  message_runtime
  roscpp
  rospy
  tf
  cmake_modules         #https://github.com/ros/cmake_modules/blob/0.3-devel/README.md#usage
  roslaunch              #http://wiki.ros.org/roslaunch#Catkin
)

## System dependencies are found with CMake's conventions

find_package(PkgConfig)
find_package(vicon_bridge)
pkg_check_modules( EIGEN3 REQUIRED eigen3 )
include_directories( ${EIGEN3_INCLUDE_DIRS} )

add_definitions(${EIGEN_DEFINITIONS})


find_package(Boost REQUIRED COMPONENTS system serialization thread)
if(Boost_FOUND)
  include_directories(${Boost_INCLUDE_DIRS})
  include_directories(${Boost_INCLUDE_DIR})
  LINK_DIRECTORIES(${Boost_LIBRARY_DIR})
  LINK_DIRECTORIES(${Boost_LIBRARY_DIR})
endif()

find_package(cmake_modules REQUIRED)

## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
# catkin_python_setup()

################################################
## Declare ROS messages, services and actions ##
################################################

## To declare and build messages, services or actions from within this
## package, follow these steps:
## * Let MSG_DEP_SET be the set of packages whose message types you use in
##   your messages/services/actions (e.g. std_msgs, actionlib_msgs, ...).
## * In the file package.xml:

##   * add a build_depend tag for "message_generation"
##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependency has been pulled in
##     but can be declared for certainty nonetheless:

##   * add a build_depend and a run_depend tag for each package in MSG_DEP_SET
##   * If MSG_DEP_SET isn't empty the following dependencies might have been
##     pulled in transitively but can be declared for certainty nonetheless:
##     * add a build_depend tag for "message_generation"

##     * add a run_depend tag for "message_runtime"
## * In this file (CMakeLists.txt):
##   * add "message_generation" and every package in MSG_DEP_SET to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * add "message_runtime" and every package in MSG_DEP_SET to
##     catkin_package(CATKIN_DEPENDS ...)
##   * uncomment the add_*_files sections below as needed
##     and list every .msg/.srv/.action file to be processed
##   * uncomment the generate_messages entry below
##   * add every package in MSG_DEP_SET to generate_messages(DEPENDENCIES ...)

## Generate messages in the 'msg' folder

# add_message_files(
#   FILES
#   Message1.msg
#   Message2.msg
# )

## Generate services in the 'srv' folder
# add_service_files(
#   FILES
#   Service1.srv
#   Service2.srv
# )

#add_message_files(
#  FILES
#  Marker.msg
#  Markers.msg
#  TfDistortInfo.msg
#)

## Generate services in the 'srv' folder
#add_service_files(
#  FILES
#  viconCalibrateSegment.srv
#  viconGrabPose.srv
#)

## Generate actions in the 'action' folder
# add_action_files(
#   FILES
#   Action1.action
#   Action2.action
# )

## Generate added messages and services with any dependencies listed here

#generate_messages(
#   DEPENDENCIES
#  geometry_msgs
# )

################################################
## Declare ROS dynamic reconfigure parameters ##
################################################

## To declare and build dynamic reconfigure parameters within this
## package, follow these steps:
## * In the file package.xml:
##   * add a build_depend and a run_depend tag for "dynamic_reconfigure"
## * In this file (CMakeLists.txt):
##   * add "dynamic_reconfigure" to
##     find_package(catkin REQUIRED COMPONENTS ...)
##   * uncomment the "generate_dynamic_reconfigure_options" section below
##     and list every .cfg file to be processed

## Generate dynamic reconfigure parameters in the 'cfg' folder
# generate_dynamic_reconfigure_options(
#   cfg/DynReconf1.cfg
#   cfg/DynReconf2.cfg

#generate_messages(
# DEPENDENCIES
#  geometry_msgs
#)

###################################
## catkin specific configuration ##
###################################
## The catkin_package macro generates cmake config files for your package
## Declare things to be passed to dependent projects
## INCLUDE_DIRS: uncomment this if you package contains header files
## LIBRARIES: libraries you create in this project that dependent projects also need
## CATKIN_DEPENDS: catkin_packages dependent projects also need
## DEPENDS: system dependencies of this project that dependent projects also need
catkin_package(
  INCLUDE_DIRS include
   CATKIN_DEPENDS diagnostic_updater dynamic_reconfigure geometry_msgs roscpp tf message_runtime 
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
# include_directories(include)
include_directories(${catkin_INCLUDE_DIRS})

# check for platform
if(CMAKE_SIZEOF_VOID_P MATCHES "8")
    set(VICON_SDK         {PROJECT_SOURCE_DIR}/vicon_sdk/vicon_datastream_SDK_1.2.59611_x64)
    #set(VICON_SDK             vicon/vicon_sdk/vicon_datastream_SDK_1.2.59611_x64)
    set(VICON_SDK_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/vicon_sdk/${VICON_SDK})
    set(VICON_SDK_LINK_DIR    ${PROJECT_SOURCE_DIR}/vicon_sdk/${VICON_SDK})
elseif(CMAKE_SIZEOF_VOID_P MATCHES "4")
    set(VICON_SDK         {PROJECT_SOURCE_DIR}/vicon_sdk/vicon_datastream_SDK_1.2.59611_x64)
    #set(VICON_SDK             vicon/vicon_sdk/vicon_datastream_SDK_1.2.59611_x64)
    set(VICON_SDK_INCLUDE_DIR ${PROJECT_SOURCE_DIR}/vicon_sdk/${VICON_SDK})
    set(VICON_SDK_LINK_DIR    ${PROJECT_SOURCE_DIR}/vicon_sdk/${VICON_SDK})
else(CMAKE_SIZEOF_VOID_P MATCHES "8")
    message(FATAL_ERROR "unable to detect platform")
endif (CMAKE_SIZEOF_VOID_P MATCHES "8")


set(VICON_LISTENER_INCLUDE_DIR ${vicon_listener_SOURCE_DIR}/include)

include_directories(${INCLUDE_DIRECTORIES} ${VICON_SDK_INCLUDE_DIR} 
                                            ${VICON_LISTENER_INCLUDE_DIR})

include_directories(include
                    ${catkin_INCLUDE_DIRS}
                    ${vicon_bridge_INCLUDE_DIRS})


link_directories(${LINK_DIRECTORIES} ${PROJECT_SOURCE_DIR}/lib ${VICON_SDK_LINK_DIR})


message("\n\nBoost Include  Directories: " ${Boost_INCLUDE_DIRS}
        "\n\nBoost Library Directories: \t"  ${Boost_LIBRARY_DIRS}
        "\n\nBOOST Libraries:\t\t "    ${Boost_LIBRARIES} 
        "\n\nVicon Listener Include Directories: \t\t"  ${VICON_LISTENER_INCLUDE_DIR}
      )

## Declare a C++ library
# add_library(vicon_listener
#   src/${PROJECT_NAME}/vicon_listener.cpp
# )

## Add cmake target dependencies of the library
## as an example, code may need to be generated before libraries
## either from message generation or dynamic reconfigure
# add_dependencies(vicon_listener ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Declare a C++ executable
add_executable(vicon_listener src/vicon_listener.cpp)

## Add cmake target dependencies of the executable
## same as for the library above
# add_dependencies(vicon_listener ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

## Specify libraries to link a library or executable target against
target_link_libraries(vicon_listener  ${catkin_LIBRARIES})
target_link_libraries(vicon_listener ${Boost_LIBRARIES})      #This includes the serialization libraries

## Declare a cpp library
# add_library(vicon_bridge
#   src/${PROJECT_NAME}/vicon_bridge.cpp
# )

#############
## Install ##
#############

# all install targets should use catkin DESTINATION variables
# See http://ros.org/doc/api/catkin/html/adv_user_guide/variables.html

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
# install(PROGRAMS
#   scripts/my_python_script
#   DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

# install(TARGETS vicon_listener vicon_listener

#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

## Mark cpp header files for installation
# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
#   FILES_MATCHING PATTERN "*.h"
#   PATTERN ".svn" EXCLUDE
# )
