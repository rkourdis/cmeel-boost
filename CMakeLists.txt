cmake_minimum_required(VERSION 3.20)

set(Boost_VERSION_STRING 1.79.0)

project(cmeel-boost VERSION ${Boost_VERSION_STRING})

# helper strings for Boost
string(REPLACE "." "_" Boost_VERSION_UNDERSCORES ${Boost_VERSION_STRING})
set(Boost_ARCHIVE "boost_${Boost_VERSION_UNDERSCORES}.tar.bz2")

# helper strings for boost-python configuration
execute_process(
  COMMAND ${PYTHON_EXECUTABLE} "-c" "import sys; v = sys.version_info; print(f'{v.major}.{v.minor}')"
  OUTPUT_VARIABLE PYTHON_VERSION OUTPUT_STRIP_TRAILING_WHITESPACE)
execute_process(
  COMMAND ${PYTHON_EXECUTABLE} "-c" "import distutils.sysconfig; print(distutils.sysconfig.get_python_inc())"
  OUTPUT_VARIABLE PYTHON_INCLUDE_DIR OUTPUT_STRIP_TRAILING_WHITESPACE)
configure_file(python.jam.in python.jam)

include(ExternalProject)
ExternalProject_Add(Boost
  URL "https://boostorg.jfrog.io/artifactory/main/release/${Boost_VERSION_STRING}/source/${Boost_ARCHIVE}"
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND "./bootstrap.sh" "--prefix=${CMAKE_INSTALL_PREFIX}"
  # append python configuration to generated project-config.jam
  COMMAND "sed" "-i" "-e" "1 e cat ${CMAKE_BINARY_DIR}/python.jam" "project-config.jam"
  BUILD_COMMAND "./b2" "link=shared" "python=${PYTHON_VERSION}"
  INSTALL_COMMAND "./b2" "install"
  )

# dummy file for install target
install(FILES README.md DESTINATION share/cmeel-boost/)
