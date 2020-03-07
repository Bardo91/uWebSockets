#---------------------------------------------------------------------------------------------------------------------
#  Pablo RS (a.k.a. Bardo91)
#---------------------------------------------------------------------------------------------------------------------
# 
#   CMake  Wrapper of the cool uWebSockets library
#
#---------------------------------------------------------------------------------------------------------------------

#---------------------------------------------------------------------------------------------------------------------
# General config
#---------------------------------------------------------------------------------------------------------------------

cmake_minimum_required(VERSION 3.12)

project(uWebSockets VERSION 0.14 LANGUAGES C CXX)



#---------------------------------------------------------------------------------------------------------------------
# uSockets
#---------------------------------------------------------------------------------------------------------------------
file(GLOB_RECURSE HDR_SOCKETS_FILES uSockets/src/*.h)
file(GLOB_RECURSE SRC_SOCKETS_FILES uSockets/src/*.c)
add_library(uSockets SHARED ${HDR_SOCKETS_FILES} ${SRC_SOCKETS_FILES})
set_target_properties(uSockets PROPERTIES CXX_STANDARD 17)

target_compile_definitions(uSockets PUBLIC LIBUS_USE_OPENSSL)

target_include_directories(uSockets
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/uSockets/src>
    $<INSTALL_INTERFACE:src>
)

find_package(Boost REQUIRED)
find_package(OpenSSL REQUIRED)
find_package(ZLIB)
# find_package(uv)

target_link_libraries(uSockets LINK_PUBLIC Boost::boost)
target_link_libraries(uSockets LINK_PUBLIC ZLIB::ZLIB)

target_include_directories(uSockets PUBLIC ${OPENSSL_INCLUDE_DIR})
target_link_libraries(uSockets LINK_PUBLIC ${OPENSSL_LIBRARIES})

# target_include_directories(uSockets PUBLIC ${UV_INCLUDE_DIcR})
# target_link_libraries(uSockets LINK_PUBLIC ${UV_LIBRARIES})



# ----------------------------------------------------------------------------
# Install uSockets
# ----------------------------------------------------------------------------
include(CMakePackageConfigHelpers)

## INSTALLATION C++ Library
if(NOT DEFINED CMAKE_INSTALL_PREFIX)
  set(CMAKE_INSTALL_PREFIX "/usr/local")
endif() 

install(TARGETS uSockets
        EXPORT uSocketsTargets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        INCLUDES DESTINATION ${CMAKE_INSTALL_PREFIX}
        )

write_basic_package_version_file(
  uSocketsConfigVersion.cmake
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY AnyNewerVersion
    )


# This installs the include folder
install(DIRECTORY uSockets/src DESTINATION ${CMAKE_INSTALL_PREFIX}/uSockets FILES_MATCHING PATTERN "*.h")
install(DIRECTORY uSockets/src DESTINATION ${CMAKE_INSTALL_PREFIX}/uSockets FILES_MATCHING PATTERN "*.inl")

export( TARGETS uSockets 
        NAMESPACE uNetworks:: 
        FILE uSockets.cmake)

# This generates fastcomTargets.cmake
install(EXPORT uSocketsTargets
    FILE uSocketsTargets.cmake
    NAMESPACE uNetworks::
    DESTINATION lib/cmake/uSockets
    )



configure_file(uSocketsConfig.cmake.in uSocketsConfig.cmake @ONLY)
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/uSocketsConfig.cmake"
        DESTINATION lib/cmake/uSockets
        )
    

#---------------------------------------------------------------------------------------------------------------------
# uWebSockets
#---------------------------------------------------------------------------------------------------------------------
file(GLOB_RECURSE HDR_FILES fuzzing/*.h src/*.h)
file(GLOB_RECURSE SRC_FILES fuzzing/*.cpp src/*.cpp)
add_library(uWebSockets SHARED ${HDR_FILES} ${SRC_FILES})
set_target_properties(uWebSockets PROPERTIES CXX_STANDARD 17)
target_include_directories(uWebSockets
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:src>
)
target_link_libraries(uWebSockets LINK_PUBLIC uSockets)


# ----------------------------------------------------------------------------
# Install uWebSockets
# ----------------------------------------------------------------------------

install(TARGETS uWebSockets
        EXPORT uWebSocketsTargets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        INCLUDES DESTINATION ${CMAKE_INSTALL_PREFIX}
        )

write_basic_package_version_file(
    uWebSocketsConfigVersion.cmake
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY AnyNewerVersion
    )


# This installs the include folder
install(DIRECTORY src DESTINATION ${CMAKE_INSTALL_PREFIX}/uWebSockets FILES_MATCHING PATTERN "*.h")
install(DIRECTORY src DESTINATION ${CMAKE_INSTALL_PREFIX}/uWebSockets FILES_MATCHING PATTERN "*.inl")

export( TARGETS uWebSockets 
        NAMESPACE uNetworks:: 
        FILE uWebSockets.cmake)

# This generates fastcomTargets.cmake
install(EXPORT uWebSocketsTargets
    FILE uWebSocketsTargets.cmake
    NAMESPACE uNetworks::
    DESTINATION lib/cmake/uWebSockets
    )

configure_file(uWebSocketsConfig.cmake.in uWebSocketsConfig.cmake @ONLY)
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/uWebSocketsConfig.cmake"
        DESTINATION lib/cmake/uWebSockets
        )
    