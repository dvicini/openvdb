cmake_minimum_required(VERSION 2.8)

project(openvdb)

# this is where my modules are located, change per site
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} E:/library/development/CMake/Modules)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})

# used by other sub projects
set(OPENVDB_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR})

add_subdirectory(openvdb)
#add_subdirectory(openvdb_houdini/houdini)
#add_subdirectory(openvdb_houdini/maya)
