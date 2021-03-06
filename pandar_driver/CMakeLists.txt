cmake_minimum_required(VERSION 2.8.3)
project(pandar_driver)

# Set minimum C++ standard to C++11
if (NOT "${CMAKE_CXX_STANDARD_COMPUTED_DEFAULT}")
    message(STATUS "Changing CXX_STANDARD from C++98 to C++11")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
elseif ("${CMAKE_CXX_STANDARD_COMPUTED_DEFAULT}" STREQUAL "98")
    message(STATUS "Changing CXX_STANDARD from C++98 to C++11")
    set(CMAKE_CXX_STANDARD 11)
endif()

set(${PROJECT_NAME}_CATKIN_DEPS
        diagnostic_updater
        dynamic_reconfigure
        nodelet
        roscpp
        tf
        pandar_msgs
        )

find_package(catkin REQUIRED COMPONENTS ${${PROJECT_NAME}_CATKIN_DEPS} roslint)

# This driver uses Boost threads
find_package(Boost REQUIRED COMPONENTS thread)

# libpcap provides no pkg-config or find_package module:
set(libpcap_LIBRARIES -lpcap)

include_directories(include ${Boost_INCLUDE_DIR} ${catkin_INCLUDE_DIRS})

# Generate dynamic_reconfigure server
generate_dynamic_reconfigure_options(cfg/PandarNode.cfg)

# objects needed by other ROS packages that depend on this one
catkin_package(CATKIN_DEPENDS ${${PROJECT_NAME}_CATKIN_DEPS}
        INCLUDE_DIRS include
        LIBRARIES pandar_input)

# compile the driver and input library
add_subdirectory(src/input)
add_subdirectory(src/driver)

install(DIRECTORY include/
        DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})
install(FILES nodelet_pandar.xml
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})
install(DIRECTORY launch/
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/launch)


