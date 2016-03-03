message(STATUS "---configuring openvdb_print executeable---")

file(GLOB H_FILES *.h)
file(GLOB CXX_FILES *.cc)

set(SOURCE_FILES ${H_FILES} ${CXX_FILES})

add_executable(openvdb_print ${SOURCE_FILES})

target_link_libraries(openvdb_print openvdb) 

if (WIN32)
    set_target_properties(openvdb_print
        PROPERTIES
        COMPILE_FLAGS "/bigobj")
endif()

install(TARGETS openvdb_print DESTINATION bin)
