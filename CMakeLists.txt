# This CMake file will build Freetype and Harfbuzz as external
# projects. We follow the build description as described here:
# https://sourceforge.net/projects/freetype/files/freetype2/2.5.3/ So,
# first we build Freetype2 w/o Harfbuzz, then we build Harfbuzz with
# freetype support after which we rebuild Freetype2 again.
#
# Both CMake files of Freetype2 and Harfbuzz are depending on
# pkg-config to find the dependencies for both projects. I've
# included a patch for Freetype2 and Harfbuzz which allows you to
# build Freetype2 and Harbuzz with pure CMake features. So I removed
# the dependency of pkg-config.

cmake_minimum_required(VERSION 3.4)
project(mqttpahoclient)
include(ExternalProject)

ExternalProject_Add(
  pahomqttc
  GIT_REPOSITORY https://github.com/eclipse/paho.mqtt.c.git
  # GIT_SHALLOW 1
  GIT_TAG v1.3.0 
  CMAKE_ARGS -DCMAKE_PREFIX_PATH=${CMAKE_INSTALL_PREFIX}} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR>
  UPDATE_COMMAND ""
  )

ExternalProject_Get_Property(pahomqttc install_dir)
# set(pahomqttc_install_dir install_dir)

# ExternalProject_Add(
#   harfbuzz
#   DEPENDS freetype2a
#   #PATCH_COMMAND git apply ${CMAKE_CURRENT_LIST_DIR}/harfbuzz_freetype_fix.patch
#   PATCH_COMMAND patch -p1 < ${CMAKE_CURRENT_LIST_DIR}/harfbuzz_freetype_fix.patch
#   URL https://github.com/behdad/harfbuzz/releases/download/1.4.7/harfbuzz-1.4.7.tar.bz2
#   CMAKE_ARGS -DCMAKE_PREFIX_PATH=${freetype2a_install_dir} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} -DHB_HAVE_FREETYPE=On 
#   UPDATE_COMMAND ""
#   )

# ExternalProject_Add(
#   freetype2b
#   GIT_REPOSITORY git://git.savannah.gnu.org/freetype/freetype2.git
#   GIT_SHALLOW 1
#   GIT_TAG 7e50824288fac5a36c2938fdb3e1c949ea53f982
#   PATCH_COMMAND git apply ${CMAKE_CURRENT_LIST_DIR}/freetype_harfbuzz_fix.patch
#   CMAKE_ARGS -DCMAKE_PREFIX_PATH=${CMAKE_INSTALL_PREFIX} -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE} -DCMAKE_INSTALL_PREFIX=${CMAKE_INSTALL_PREFIX} -DWITH_PNG=Off
#   UPDATE_COMMAND ""
#   )


INCLUDE_DIRECTORIES(
  .
  ${CMAKE_SOURCE_DIR}/src
  ${CMAKE_INSTALL_PREFIX}/include
  # ${pahomqttc_install_dir}/include
  ${install_dir}/include
)

  # There are multiple libmqtt versions
  # libmqttv3a.so - asynchronous
  # libmqttv3as.so - asynchronous with SSL
  # libmqttv3c.so - "classic" / synchronous
  # libmqttv3cs.so - "classic" / synchronous with SSL

if (WIN32)
  ADD_DEFINITIONS(/DCMAKE_BUILD /D_CRT_SECURE_NO_DEPRECATE)
  if (CMAKE_BUILD_TYPE STREQUAL "Release")
    list(APPEND libs ${CMAKE_INSTALL_PREFIX}/lib/mqtt3a.lib)
  else()
    list(APPEND libs ${CMAKE_INSTALL_PREFIX}/lib/mqtt3ad.lib)
  endif()
endif()

if (UNIX)
  list(APPEND libs
    # ${CMAKE_INSTALL_PREFIX}/lib/libpaho-mqttv3a.so
    ${install_dir}/lib/libpaho-mqtt3a.so
    #-lz
  )
endif()

# if (APPLE)
#   find_library(fr_coretext CoreText)
#   find_library(fr_corefoundation CoreFoundation)
#   find_library(fr_coregraphics CoreGraphics)
#   list(APPEND libs
#     ${fr_coretext}
#     ${fr_corefoundation}
#     ${fr_coregraphics}
#     )
# endif()

add_executable(paho_c_pub src/paho_c_pub.c src/pubsub_opts.c)
install(TARGETS DESTINATION bin)
add_dependencies(paho_c_pub pahomqttc)
TARGET_LINK_LIBRARIES(paho_c_pub ${libs})