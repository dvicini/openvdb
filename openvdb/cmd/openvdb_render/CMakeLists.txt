message(STATUS "---configuring openvdb_render executeable---")

file(GLOB H_FILES *.h)
file(GLOB CXX_FILES *.cc)
set(SOURCE_FILES ${H_FILES} ${CXX_FILES})

set(OpenEXR_ROOT "C:/Program Files/OpenEXR")

find_package(openexr REQUIRED)

if(OPENEXR_FOUND)
  message(STATUS "OPENEXR FOUND: " ${OPENEXR_FOUND})
  message(STATUS "OPENEXR INCLUDE DIRS: " ${OPENEXR_INCLUDE_DIRS})
  message(STATUS "OPENEXR LIBRARIES: " ${OPENEXR_LIBRARIES})
endif()

include_directories(${OPENEXR_INCLUDE_DIRS})

add_definitions() 

add_executable(openvdb_render ${SOURCE_FILES})

target_link_libraries(openvdb_render openvdb
	${ILMBASE_IlmThread_LIBRARY}
	${ILMBASE_Iex_LIBRARY}
	${OPENEXR_IlmImf_LIBRARY}
	)

# if(WIN32)
#   set_target_properties(openvdb_render
#       PROPERTIES
#       COMPILE_FLAGS "/bigobj")
# endif()

install(TARGETS openvdb_render DESTINATION bin)
