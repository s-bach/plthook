cmake_minimum_required(VERSION 3.1.3 FATAL_ERROR)
project(plthook)

set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR}/cmake)
if(NOT CACHE_DIR)
	set(CACHE_DIR "${CMAKE_CURRENT_SOURCE_DIR}/cache")
endif()

add_library(plthook INTERFACE)

# plthook
target_include_directories(plthook INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})

if(UNIX)
	if(APPLE)
		target_sources(plthook INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/plthook_osx.c)
	else()
		target_sources(plthook INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/plthook_elf.c)
	endif()
elseif(WIN32)
	target_sources(plthook INTERFACE ${CMAKE_CURRENT_SOURCE_DIR}/plthook_win32.c)
endif()
# let remote project know which libraries need to be linked

set(MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")
set(BUILD_CMAKE_DIR "${CMAKE_CURRENT_BINARY_DIR}/cmake")

# export targets for remote projects (i.e. make find_package(gl3w) work)
configure_file("${MODULE_PATH}/plthook-config.cmake" "${BUILD_CMAKE_DIR}/plthook-config.cmake" COPYONLY)

export(TARGETS plthook FILE "${BUILD_CMAKE_DIR}/plthook-targets.cmake")

export(PACKAGE plthook)
