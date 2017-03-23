#
# CMakeLists.txt for sparsehash
#
# Copyright 2015, Aaditya Kalsi
#

cmake_minimum_required(VERSION 2.8)

#
# sparsehash is header only, so this is an easy CMakeLists.txt
#
project( sparsehash )

if(NOT WIN32)
  # copy all the source to the build directory
  file(GLOB_RECURSE entries ${CMAKE_SOURCE_DIR}/*)
  foreach(entry ${entries})
    file(RELATIVE_PATH relentry ${CMAKE_SOURCE_DIR} ${entry})
    get_filename_component(parent ${CMAKE_BINARY_DIR}/${relentry} DIRECTORY)
    file(COPY ${entry} DESTINATION ${parent})
  endforeach()
  add_custom_command(
    OUTPUT source.copy.done
    COMMAND ${CMAKE_COMMAND} -E echo "Copy done.")
  add_custom_command(
    OUTPUT configure.done
    COMMAND ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    DEPENDS source.copy.done)
  add_custom_command(
    OUTPUT build.done
    COMMAND make -j
    DEPENDS configure.done)
  add_custom_command(
    OUTPUT install
    COMMAND make install
    DEPENDS build.done)
else()# Windows
  file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/google DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/sparsehash DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/windows/google DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/windows/sparsehash DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  add_custom_target(install COMMAND ${CMAKE_COMMAND} -E echo "Install done.")
endif()

