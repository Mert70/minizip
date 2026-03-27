#***************************************************************************
# Copyright (C) 2020 Robbert E. Peters (GizMoCuz)
#***************************************************************************
cmake_minimum_required(VERSION 3.5...3.28)

project("minizip" C)

INCLUDE(FindZLIB)

IF(NOT ZLIB_FOUND)
	message(FATAL_ERROR "zlib library is required for libminizip!")
ENDIF(NOT ZLIB_FOUND)

add_library(minizip_static  STATIC ioapi.c unzip.c zip.c)
add_library(minizip_shared SHARED ioapi.c unzip.c zip.c)

add_library(minizip::minizip ALIAS minizip_static)
add_library(minizip::minizip_shared ALIAS minizip_shared)

foreach(target IN ITEMS minizip_static minizip_shared)
	target_link_directories(${target} PUBLIC ${ZLIB_LIBRARIES})
	target_link_libraries(${target} PUBLIC minizip ${ZLIB_LIBRARIES})
	
	target_include_directories(${target} PUBLIC ${ZLIB_INCLUDE_DIR})
	target_include_directories(${target} PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
endforeach()
