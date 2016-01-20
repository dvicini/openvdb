message(STATUS "---configuring openvdb_view executeable---")

file(GLOB H_FILES *.h
	${OPENVDB_INCLUDE_DIR}/openvdb/viewer/*.h)
file(GLOB CXX_FILES *.cc
	${OPENVDB_INCLUDE_DIR}/openvdb/viewer/*.cc)

set(SOURCE_FILES ${H_FILES} ${CXX_FILES})

find_package(opengl)
find_package(glew)
find_package(glfw)

include_directories(
	${OPENVDB_INCLUDE_DIR}
	${GLFW_INCLUDE_DIRS}
	${GLEW_INCLUDE_PATH})

add_definitions(-D_USE_MATH_DEFINES -DOPENVDB_USE_GLFW_3 -DGLEW_STATIC) 

add_executable(openvdb_view ${SOURCE_FILES})

target_link_libraries(openvdb_view openvdb
    ${OPENGL_LIBRARIES}
    ${GLFW_LIBRARIES}
	${GLEW_LIBRARY})

if(WIN32)
  set_target_properties(openvdb_view
      PROPERTIES
      COMPILE_FLAGS "/bigobj")
endif()

install(TARGETS openvdb_view DESTINATION bin)
