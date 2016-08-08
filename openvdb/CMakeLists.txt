message(STATUS "---configuring openvdb library---")

#set(BOOST_ROOT "C:/Boost/boost_1_59_0")
#set(BOOST_INCLUDEDIR "C:/Boost/boost_1_59_0")
#set(BOOST_LIBRARYDIR "C:/Boost/boost_1_59_0/lib")

set(Boost_DEBUG OFF)
set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)

find_package(Boost 1.59.0 REQUIRED COMPONENTS iostreams system thread filesystem)

if(Boost_FOUND)
  message(STATUS "BOOST FOUND: " ${Boost_FOUND})
  message(STATUS "BOOST VERSION: " ${Boost_VERSION})
endif()

#set(ILMBase_ROOT "C:/Program Files/ILMBase")
message(STATUS "ilmbase root is " ${ILMBase_ROOT})

find_package(ILMBase REQUIRED)

if(ILMBASE_FOUND)
  message(STATUS "ILMBASE FOUND: " ${ILMBASE_FOUND})
  message(STATUS "ILMBASE VERSION: " ${ILMBASE_VERSION})
endif()

#set(TBB_ROOT_DIR "e:/library/development/tbb/tbb44_20160526oss")

find_package(TBB REQUIRED)

if(TBB_FOUND)
  message(STATUS "TBB FOUND: " ${TBB_FOUND})
  message(STATUS "TBB VERSION: " ${TBB_VERSION})
endif()

#set(ZLIB_ROOT "C:/Program Files/ZLIB")

find_package(ZLIB REQUIRED)

if(ZLIB_FOUND)
  message(STATUS "ZLIB FOUND: " ${ZLIB_FOUND})
  message(STATUS "ZLIB VERSION: " ${ZLIB_VERSION_STRING})
endif()

#set(BLOSC_ROOT "C:/Program Files/blosc")

find_package(Blosc)

if(BLOSC_FOUND)
  message(STATUS "BLOSC FOUND: " ${BLOSC_FOUND})
  message(STATUS "BLOSC VERSION: " ${BLOSC_VERSION})
endif()

# collect source files
file(GLOB H_FILES *.h
    io/*.h
    math/*.h
    tools/*.h
    metadata/*.h
    tree/*.h
    util/*.h)

file(GLOB CXX_FILES *.cc
    io/*.cc
    math/*.cc
    metadata/*.cc
    util/*.cc)

set(SOURCE_FILES ${H_FILES} ${CXX_FILES})

if(WIN32)
  add_definitions(-DNOMINMAX -DOPENVDB_STATICLIB
      -DOPENVDB_PRIVATE -DHALF_EXPORTS
      -DOPENVDB_DLL -DBOOST_ALL_NO_LIB
      -DOPENVDB_USE_BLOSC)
endif()

include_directories(
    ${ILMBASE_INCLUDE_DIRS}
    ${Boost_INCLUDE_DIRS}
    ${TBB_INCLUDE_DIRS}
    ${ZLIB_INCLUDE_DIRS}
    ${BLOSC_INCLUDE_DIRS})

add_library(openvdb ${SOURCE_FILES})

target_link_libraries(openvdb
	${Boost_LIBRARIES}
	${ZLIB_LIBRARIES}
  optimized ${ILMBASE_Half_LIBRARY}
	debug ${ILMBASE_LIBRARY_DIRS}/Halfd.lib
	${TBB_LIBRARIES}
  optimized ${BLOSC_STATIC_LIBRARY}
  debug ${BLOSC_DEBUG_STATIC_LIBRARY})

if(WIN32)
  set_target_properties(openvdb
      PROPERTIES
      COMPILE_FLAGS "/bigobj")
endif()

install(TARGETS openvdb DESTINATION lib)
#INSTALL(FILES ${H_FILES} DESTINATION include/openvdb)

foreach(HEADER ${H_FILES})
  get_filename_component(DIR ${HEADER} PATH)
  get_filename_component(SUBDIR ${DIR} NAME)
  if(SUBDIR STREQUAL "openvdb")
  install(FILES ${HEADER} DESTINATION
        include/openvdb)
  else()
  install(FILES ${HEADER} DESTINATION
        include/openvdb/${SUBDIR})
  endif()
endforeach(HEADER HEADERS)

add_subdirectory(cmd/openvdb_print)
add_subdirectory(cmd/openvdb_view)
add_subdirectory(cmd/openvdb_render)
add_subdirectory(unittest)
