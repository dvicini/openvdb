message(STATUS "---configuring openvdb library---")

set(BOOST_ROOT "C:/Boost/boost_1_57_0")
set(BOOST_INCLUDEDIR "C:/Boost/boost_1_57_0")
set(BOOST_LIBRARYDIR "C:/Boost/boost_1_57_0/lib")

set(Boost_DEBUG OFF)
set(Boost_USE_STATIC_LIBS ON)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)

find_package(boost 1.46.0 REQUIRED COMPONENTS iostreams system thread filesystem)

if(Boost_FOUND)
  message(STATUS "BOOST FOUND: " ${Boost_FOUND})
  message(STATUS "BOOST VERSION: " ${Boost_VERSION})
  message(STATUS "BOOST INCLUDE DIRS: " ${Boost_INCLUDE_DIR})
  message(STATUS "BOOST LIBRARY DIRS: " ${Boost_LIBRARY_DIR})
  message(STATUS "BOOST LIBRARIES: " ${Boost_LIBRARIES})
  message(STATUS "BOOST system: " ${Boost_system_LIBRARY})
endif()

# this is where my modules are located, change per site
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} c:/dev_local/CMake/Modules)

set(ILMBase_ROOT "C:/Program Files/ILMBase")

find_package(ILMBase REQUIRED)

if(ILMBASE_FOUND)
  message(STATUS "ILMBASE FOUND: " ${ILMBASE_FOUND})
  message(STATUS "ILMBASE VERSION: " ${ILMBASE_VERSION})
  message(STATUS "ILMBASE INCLUDE DIRS: " ${ILMBASE_INCLUDE_DIRS})
  message(STATUS "ILMBASE LIBRARY DIRS: " ${ILMBASE_LIBRARY_DIRS})
  message(STATUS "ILMBASE LIBRARIES: " ${ILMBASE_LIBRARIES})
  message(STATUS "ILMBASE HALF LIB: " ${ILMBASE_Half_LIBRARY})
endif()

# using FindTBB.cmake from Pixar's OpenSubdiv project
# https://github.com/PixarAnimationStudios/OpenSubdiv
set(TBB_LOCATION "c:/dev_local/tbb/tbb41_20130116oss")

find_package(tbb REQUIRED)

if(TBB_FOUND)
  message(STATUS "TBB FOUND: " ${TBB_FOUND})
  message(STATUS "TBB VERSION: " ${TBB_VERSION})
  message(STATUS "TBB INCLUDE DIR: " ${TBB_INCLUDE_DIR})
  message(STATUS "TBB LIBRARIES: " ${TBB_LIBRARIES})
endif()

set(ZLIB_ROOT "C:/Program Files/ZLIB")

find_package(zlib REQUIRED)

if(ZLIB_FOUND)
  message(STATUS "ZLIB FOUND: " ${ZLIB_FOUND})
  message(STATUS "ZLIB VERSION: " ${ZLIB_VERSION_STRING})
  message(STATUS "ZLIB INCLUDE DIRS: " ${ZLIB_INCLUDE_DIRS})
  message(STATUS "ZLIB LIBRARIES: " ${ZLIB_LIBRARIES})
endif()

set(BLOSC_ROOT "C:/Program Files/blosc")

find_package(blosc)

if(BLOSC_FOUND)
  message(STATUS "BLOSC FOUND: " ${BLOSC_FOUND})
  message(STATUS "BLOSC INCLUDE DIR: " ${BLOSC_INCLUDE_DIR})
  message(STATUS "BLOSC LIBRARY: " ${BLOSC_LIBRARY})
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
    ${TBB_INCLUDE_DIR}
    ${ZLIB_INCLUDE_DIRS}
    ${BLOSC_INCLUDE_DIR})

add_library(openvdb ${SOURCE_FILES})

target_link_libraries(openvdb
    optimized ${BOOST_LIBRARYDIR}/libboost_iostreams-vc110-mt-1_57.lib
    optimized ${BOOST_LIBRARYDIR}/libboost_system-vc110-mt-1_57.lib
    optimized ${BOOST_LIBRARYDIR}/libboost_thread-vc110-mt-1_57.lib
    ${ILMBASE_Half_LIBRARY}
    optimized ${TBB_tbb_LIBRARY}
    optimized ${TBB_tbbmalloc_LIBRARY}
    optimized ${TBB_tbbmalloc_proxy_LIBRARY}
  debug ${TBB_tbb_debug_LIBRARY}
    debug ${TBB_tbbmalloc_debug_LIBRARY}
    debug ${TBB_tbbmalloc_proxy_debug_LIBRARY}
    ${ZLIB_LIBRARIES}
    ${BLOSC_LIBRARY}
  debug ${BOOST_LIBRARYDIR}/libboost_iostreams-vc110-mt-gd-1_57.lib
    debug ${BOOST_LIBRARYDIR}/libboost_system-vc110-mt-gd-1_57.lib
    debug ${BOOST_LIBRARYDIR}/libboost_thread-vc110-mt-gd-1_57.lib)

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
