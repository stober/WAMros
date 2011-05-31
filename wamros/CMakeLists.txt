#WAMROS
#Dang - EPFL

#Boilerplate-----------------------------------------
cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)
rosbuild_init()
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
#----------------------------------------------------
set(CMAKE_VERBOSE_MAKEFILE true)

#don't need since they are provided by the wam_msgs package
#we have both messages and services
#rosbuild_genmsg()
#rosbuild_gensrv()

# Use find_path to locate btclient include directory. Not that setting BTCLIENT_INC environment variable will also work.
find_path(BTCLIENT_INC_DIR btwam.h ENV BTCLIENT_INC /usr/include /usr/local/include /usr/include/btclient /usr/local/include/btclient)
message("BTCLIENT_INC_DIR: " ${BTCLIENT_INC_DIR})
include_directories(${BTCLIENT_INC_DIR})

# Similarly use find_path to find the btclient libraries to link against.
find_path(BTCLIENT_LIB_DIR libbtwam.a ENV BTCLIENT_LIB /usr/lib /usr/local/lib /usr/lib/btclient /usr/local/lib/btclient)
message("BTCLIENT_LIB_DIR: " ${BTCLIENT_LIB_DIR})
link_directories(${BTCLIENT_LIB_DIR})

#Fine Xenomai
find_path(XENOMAI_INC_DIR xeno_config.h ENV XENOMAI_INC_DIR /usr/include /usr/local/include /usr/xenomai/include)
message("XENOMAI_INC_DIR: " ${XENOMAI_INC_DIR})
include_directories(${XENOMAI_INC_DIR})

find_path(XENOMAI_LIB_DIR libxenomai.so ENV XENOMAI_LIB_DIR /usr/lib /usr/local/lib /usr/xenomai/lib)
message("XENOMAI_LIB_DIR: " ${XENOMAI_LIB_DIR})
link_directories(${XENOMAI_LIB_DIR})

#Here's what we're making
rosbuild_add_executable(wamros_node src/wamros_node.cpp src/WamNode.cpp)

#Flags ---------------------------------------------

#These may not be necessary
rosbuild_add_link_flags(wamros_node -Wl -rdynamic --cref)
rosbuild_add_compile_flags(wamros_node -Wall -pipe)

#These are necessary
rosbuild_add_compile_flags(wamros_node -DXENOMAI)  #Tells bt headers

#Linking----------------------------------
target_link_libraries(wamros_node btwam pthread m btsystem curses)
target_link_libraries(wamros_node xenomai native) #might change if not xenomai
target_link_libraries(wamros_node pcan) #might change if not pcan

