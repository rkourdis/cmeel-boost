cmake_minimum_required(VERSION 3.20)

set(Boost_VERSION_STRING 1.80.0)

project(cmeel-boost VERSION ${Boost_VERSION_STRING})

# helper strings for Boost
string(REPLACE "." "_" Boost_VERSION_UNDERSCORES ${Boost_VERSION_STRING})
set(Boost_ARCHIVE "boost_${Boost_VERSION_UNDERSCORES}.tar.bz2")

find_package(Python3 REQUIRED)

configure_file(python.jam.in python.jam)

set(BUILD_COMMAND
  "./b2"
  "hardcode-dll-paths=true"
  "dll-path='$ORIGIN'"
  "link=shared"
  "python=3.${Python3_VERSION_MINOR}"
  )

include(ExternalProject)
ExternalProject_Add(Boost
  URL "https://boostorg.jfrog.io/artifactory/main/release/${Boost_VERSION_STRING}/source/${Boost_ARCHIVE}"
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND "./bootstrap.sh" "--prefix=${CMAKE_INSTALL_PREFIX}"
  # append python configuration to generated project-config.jam
  COMMAND "sed" "-i" "-e" "1 e cat ${CMAKE_BINARY_DIR}/python.jam" "project-config.jam"
  BUILD_COMMAND ${BUILD_COMMAND}
  INSTALL_COMMAND ${BUILD_COMMAND} "install"
  )

# dummy file for install target
install(FILES README.md DESTINATION share/cmeel-boost/)
