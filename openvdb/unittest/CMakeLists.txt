message(STATUS "---configuring openvdb_test executeable---")

file(GLOB H_FILES *.h)
file(GLOB CXX_FILES *.cc)

find_package(CppUnit)

if(CPPUNIT_FOUND)
  message(STATUS "CPPUNIT FOUND: " ${CPPUNIT_FOUND})
endif()

include_directories(
    ${OPENVDB_INCLUDE_DIR}
    ${CPPUNIT_INCLUDE_DIR})

set(SOURCE_FILES ${H_FILES} ${CXX_FILES})

add_executable(openvdb_test ${SOURCE_FILES})

if(CMAKE_BUILD_TYPE MATCHES "(Debug|DEBUG|debug|RelWithDebInfo|RELWITHDEBINFO|relwithdebinfo)")
  set(CPPUNIT_DEBUG_BUILD TRUE)
else()
  set(CPPUNIT_DEBUG_BUILD FALSE)
endif()

if (CPPUNIT_DEBUG_BUILD)
  target_link_libraries(openvdb_test openvdb
      ${Boost_LIBRARIES}
	  ${CPPUNIT_DEBUG_LIBRARY}
      )
else()
  target_link_libraries(openvdb_test openvdb
      ${Boost_LIBRARIES}
      ${CPPUNIT_LIBRARY}
      )
endif()


if(WIN32)
    set_target_properties(openvdb_test
        PROPERTIES
        COMPILE_FLAGS "/bigobj")
endif()

install(TARGETS openvdb_test DESTINATION bin)
