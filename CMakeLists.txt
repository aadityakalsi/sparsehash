#
# CMakeLists.txt for sparsehash
#
# Copyright 2015, Aaditya Kalsi
#

cmake_minimum_required(VERSION 2.8)

project( sparsehash )

if(NOT WIN32)
  # copy all the source to the build directory
  file(GLOB_RECURSE files ${CMAKE_SOURCE_DIR}/*)
  foreach(entry ${files})
    file(RELATIVE_PATH relentry ${CMAKE_SOURCE_DIR} ${entry})
    set(finalloc ${CMAKE_BINARY_DIR}/${relentry}})
    get_filename_component(parent ${finalloc} DIRECTORY)
    file(COPY ${entry} DESTINATION ${parent})
  endforeach()
  add_custom_command(
    OUTPUT source.copy.done
    COMMAND ${CMAKE_COMMAND} -E echo "Copy done.")
  add_custom_command(
    OUTPUT configure.done
    COMMAND ./configure --prefix=${CMAKE_INSTALL_PREFIX}
    DEPENDS source.copy.done)
  add_custom_target(
    build.tgt
    COMMAND make
    DEPENDS configure.done)
  add_custom_target(
    install_pkg
    COMMAND make install
    COMMAND rm -fr ${CMAKE_INSTALL_PREFIX}/lib ${CMAKE_INSTALL_PREFIX}/share
    DEPENDS build.tgt)
else()# Windows
  file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/google DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/sparsehash DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/windows/google DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/windows/sparsehash DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  add_custom_target(install_pkg COMMAND ${CMAKE_COMMAND} -E echo "Install done.")
endif()