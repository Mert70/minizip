#***************************************************************************
# Copyright (C) 2020 Robbert E. Peters (GizMoCuz)
#***************************************************************************
cmake_minimum_required(VERSION 3.5...3.28)

project(minizip LANGUAGES C VERSION 0.1.0)

INCLUDE(FindZLIB)

IF(NOT ZLIB_FOUND)
	message(FATAL_ERROR "zlib library is required for libminizip!")
ENDIF(NOT ZLIB_FOUND)

file(GLOB_RECURSE MINIZIP_SOURCES ${PROJECT_SOURCE_DIR}/src/ioapi.c ${PROJECT_SOURCE_DIR}/src/unzip.c ${PROJECT_SOURCE_DIR}/src/zip.c)

add_library(minizip_static STATIC ${MINIZIP_SOURCES})
add_library(minizip_shared SHARED ${MINIZIP_SOURCES})

add_library(minizip::minizip ALIAS minizip_static)
add_library(minizip::minizip_shared ALIAS minizip_shared)

foreach(target IN ITEMS minizip_static minizip_shared)
	target_link_directories(${target} PUBLIC ${ZLIB_LIBRARIES})
	target_link_libraries(${target} PUBLIC ${ZLIB_LIBRARIES})
	
	target_include_directories(${target} PUBLIC ${ZLIB_INCLUDE_DIR})
	target_include_directories(${target} PUBLIC 
        $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>)
endforeach()

install(
    DIRECTORY ${PROJECT_SOURCE_DIR}/include/
    DESTINATION include
)

install(
    TARGETS minizip_static minizip_shared
    EXPORT minizip_Targets
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
    RUNTIME DESTINATION bin
)

install(
    EXPORT minizip_Targets
    FILE minizipConfig.cmake
    NAMESPACE minizip::
    DESTINATION lib/cmake/minizip
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/minizipConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)
install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/minizipConfigVersion.cmake
    DESTINATION lib/cmake/minizip
)
