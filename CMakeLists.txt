cmake_minimum_required(VERSION 3.20)

set(BOOST_VERSION 1.79.0)

project(cmeel-boost VERSION ${BOOST_VERSION})

execute_process(
  COMMAND ${PYTHON_EXECUTABLE} -c "import sys; v = sys.version_info; print(f'{v.major}.{v.minor}')"
  OUTPUT_VARIABLE PYTHON_VERSION OUTPUT_STRIP_TRAILING_WHITESPACE)

include(ExternalProject)
string(REPLACE "." "_" BOOST_UND ${BOOST_VERSION})
ExternalProject_Add(Boost
  URL "https://boostorg.jfrog.io/artifactory/main/release/${BOOST_VERSION}/source/boost_${BOOST_UND}.tar.bz2"
  BUILD_IN_SOURCE ON
  CONFIGURE_COMMAND "./bootstrap.sh" "--prefix=${CMAKE_INSTALL_PREFIX}"
  BUILD_COMMAND "./b2" "link=shared" "python=${PYTHON_VERSION}"
  INSTALL_COMMAND "./b2" "install"
  )
