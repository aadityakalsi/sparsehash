#
# CMakeLists.txt for sparsehash
#
# Copyright 2015, Aaditya Kalsi
#

cmake_minimum_required(VERSION 2.8)

project( sparsehash )

if(NOT WIN32)
  add_custom_command(
    OUTPUT configure.done
    COMMAND ./configure --prefix=${CMAKE_INSTALL_PREFIX})
  add_custom_target(
    build.tgt
    COMMAND make
    DEPENDS configure.done)
  add_custom_target(
    install_pkg
    COMMAND make install
    DEPENDS build.tgt)
else()# Windows
  file(MAKE_DIRECTORY ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/google DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/sparsehash DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/windows/google DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  file(COPY ${CMAKE_SOURCE_DIR}/src/windows/sparsehash DESTINATION ${CMAKE_INSTALL_PREFIX}/include)
  add_custom_target(install_pkg COMMAND ${CMAKE_COMMAND} -E echo "Install done.")
endif()