message(STATUS "---configuring openvdb_test executeable---")

file(GLOB H_FILES *.h)
file(GLOB CXX_FILES *.cc)

set(CPPUNIT_INCLUDE_DIR "e:/library/development/cppunit/cppunit-1.12.1/include")

find_package(cppunit)

if(CPPUNIT_FOUND)
  message(STATUS "CPPUNIT FOUND: " ${CPPUNIT_FOUND})
  message(STATUS "CPPUNIT INCLUDE DIR: " ${CPPUNIT_INCLUDE_DIR})
  message(STATUS "CPPUNIT LIBRARIES: " ${CPPUNIT_LIBRARIES})
endif()

include_directories(
    ${OPENVDB_INCLUDE_DIR}
    ${CPPUNIT_INCLUDE_DIR})

set(SOURCE_FILES ${H_FILES} ${CXX_FILES})

add_executable(openvdb_test ${SOURCE_FILES})

target_link_libraries(openvdb_test openvdb
    optimized ${BOOST_LIBRARYDIR}/libboost_filesystem-vc110-mt-1_57.lib
    optimized ${CPPUNIT_LIBRARY}
    debug ${BOOST_LIBRARYDIR}/libboost_filesystem-vc110-mt-gd-1_57.lib
    debug ${CPPUNIT_DEBUG_LIBRARY})

if(WIN32)
    set_target_properties(openvdb_test
        PROPERTIES
        COMPILE_FLAGS "/bigobj")
endif()

install(TARGETS openvdb_test DESTINATION bin)
