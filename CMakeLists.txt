cmake_minimum_required(VERSION 3.2)

project(bar VERSION 0.1.0 LANGUAGES CXX)
include(GNUInstallDirs)

SET(VERSION_MAJOR "0")
SET(VERSION_MINOR "1")
SET(VERSION_PATCH "0")
SET(VERSION "${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}")
ADD_DEFINITIONS(-DVERSION="${VERSION}")

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -O3")

add_library(libbar SHARED
	src/bar.cpp
	)
add_library(Bar::libbar ALIAS libbar)
target_include_directories(libbar 
	PUBLIC 
	  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	  $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
	  )
target_compile_features(libbar PUBLIC cxx_std_11)
set_target_properties(libbar
	PROPERTIES
	  OUTPUT_NAME "bar"
	)

set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/Bar)
install(TARGETS
          libbar
	EXPORT 
	  BarTargets
	ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
	LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
	RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
	)

install(EXPORT BarTargets
	FILE BarTargets.cmake
	NAMESPACE Bar::
	DESTINATION ${INSTALL_CONFIGDIR}
	)

install(DIRECTORY include/
	DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}
	FILES_MATCHING PATTERN "*.h"
	)

#####################
# ConfigVersion file
##
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
	${CMAKE_CURRENT_BINARY_DIR}/BarConfigVersion.cmake
	VERSION ${PROJECT_VERSION}
	COMPATIBILITY AnyNewerVersion
	)

configure_package_config_file(
	${CMAKE_CURRENT_LIST_DIR}/CMake/BarConfig.cmake.in
	${CMAKE_CURRENT_BINARY_DIR}/BarConfig.cmake
	INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
	)

## Install all the helper files
install(
	FILES
	  ${CMAKE_CURRENT_BINARY_DIR}/BarConfig.cmake
	  ${CMAKE_CURRENT_BINARY_DIR}/BarConfigVersion.cmake
	DESTINATION ${INSTALL_CONFIGDIR}
	)

