cmake_minimum_required(VERSION 3.3)
project(DRMPlayready)

find_package(WPEFramework)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

set(MODULE_NAME Playready)

option(NETFLIX_EXTENSION "Enable Netflix DRM" ON)

find_package(PlayReady REQUIRED)

add_library(${MODULE_NAME} SHARED
    MediaSession.cpp
    MediaSystem.cpp
    MediaSessionExt.cpp)

set_target_properties(${MODULE_NAME} PROPERTIES
        CXX_STANDARD 11
        CXX_STANDARD_REQUIRED YES)

if (NETFLIX_EXTENSION)
include("${PLAYREADY_INCLUDE}/playready.cmake")
add_compile_definitions(NETFLIX)
endif()

target_include_directories(${MODULE_NAME}
    PRIVATE
    "${CMAKE_SYSROOT}/usr/include"
    "${CMAKE_SYSROOT}/usr/include/${NAMESPACE}"
    ${PLAYREADY_INCLUDE_DIRS})

if (NETFLIX_EXTENSION)
target_link_libraries(${MODULE_NAME}
    PRIVATE
        PlayReady::PlayReady)
else()
target_link_libraries(${MODULE_NAME}
    ${PLAYREADY_LIBRARIES})
endif()

# Library installation section
set_target_properties(${MODULE_NAME} PROPERTIES SUFFIX ".drm")
set_target_properties(${MODULE_NAME} PROPERTIES PREFIX "")

install(TARGETS ${MODULE_NAME} DESTINATION ${CMAKE_INSTALL_PREFIX}/share/WPEFramework/OCDM)

