message(STATUS "---configuring openvdb_render executeable---")

file(GLOB H_FILES *.h)
file(GLOB CXX_FILES *.cc)
set(SOURCE_FILES ${H_FILES} ${CXX_FILES})

find_package(openexr REQUIRED)

if(OPENEXR_FOUND)
  message(STATUS "OPENEXR FOUND: " ${OPENEXR_FOUND})
  message(STATUS "OPENEXR VERSION: " ${OPENEXR_VERSION})
endif()

include_directories(${OPENEXR_INCLUDE_DIRS})

add_definitions() 

add_executable(openvdb_render ${SOURCE_FILES})

target_link_libraries(openvdb_render openvdb
    ${ILMBASE_IlmThread_LIBRARY}
    ${ILMBASE_Iex_LIBRARY}
    ${OPENEXR_IlmImf_LIBRARY}
    )

install(TARGETS openvdb_render DESTINATION bin)
