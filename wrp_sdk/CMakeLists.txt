cmake_minimum_required(VERSION 3.1.0)
project(wrp_sdk)

# Find catkin
find_package(catkin REQUIRED)

# generate symbols for IDE indexer
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/devel)

## Set compiler to use c++ 11 features
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

## Put all binary files into /bin and libraries into /lib
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR}/bin)
set(LIBRARY_OUTPUT_PATH ${CMAKE_BINARY_DIR}/lib)
set(ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

## Use GNUInstallDirs to install libraries into correct
# locations on all platforms.
include(GNUInstallDirs)

## Chosse build type
set(CMAKE_BUILD_TYPE Release)
# set(CMAKE_BUILD_TYPE Debug)

set(default_build_type "Release")
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Setting build type to '${default_build_type}' as none was specified.")
  set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
      STRING "Choose the type of build." FORCE)
  # Set the possible values of build type for cmake-gui
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
    "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()

## Optionally built modules: ON/OFF
set(BUILD_TESTS OFF)
set(BUILD_MONITOR ON)

# Disable monitor if ncurses library is not found
set(CURSES_NEED_NCURSES TRUE)
find_package(Curses QUIET)

if(BUILD_MONITOR AND NOT CURSES_FOUND)
    set(BUILD_MONITOR OFF)
    message(STATUS "Monitor app will not be built due to missing ncurses library, try: sudo apt install libncurses5-dev")
endif()

## Add source directories
add_subdirectory(src)

if(BUILD_TESTS)
add_subdirectory(unit_tests)
endif()

## Export catkin targets
set(EXPORTED_LIBS 
    asyncio
    stopwatch
    scout_protocol
    scoutbase
    hunter_protocol
    hunterbase
)
set(INCLUDE_DIRS 
    src/common/async_io/asio
    src/common/async_io/include
    src/common/utilities/stopwatch/include
    src/scout_sdk/scout_protocol/include
    src/scout_sdk/scout_base/include
    src/hunter_sdk/hunter_protocol/include
    src/hunter_sdk/hunter_base/include
)
catkin_package(
    LIBRARIES ${EXPORTED_LIBS}
    INCLUDE_DIRS ${INCLUDE_DIRS}
    # DEPENDS rt pthread
)

## Add catkin install targets
# install(TARGETS ${EXPORTED_LIBS}
#   RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
#   LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
#   ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION})

# install(DIRECTORY include/${PROJECT_NAME}/
#   DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION})

# install(DIRECTORY asio/asio
#   DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

# install(FILES asio/asio.hpp
#   DESTINATION ${CATKIN_GLOBAL_INCLUDE_DESTINATION})

# install(DIRECTORY scripts
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION})