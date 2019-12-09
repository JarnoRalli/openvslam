#TODO: There are still cyclic dependencies in the modules.
#These need to be fixed before shared libraries can be built.
#TODO: The project has not been tested without FetchContent

cmake_minimum_required(VERSION 3.12)
project(OpenVSLAM VERSION 2.0 LANGUAGES CXX C)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)
if( CMAKE_CXX_COMPILER_ID STREQUAL "MSVC" )
    set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} /FORCE:MULTIPLE")
endif()

enable_testing()

#---------
# Options
#---------
#By default we use FetchContent to include most of the not so common
#third party libraries in the project. By turning these configuration bits off, we can
#use normal find_package mechanism.
option(BUILD_SHARED_LIBS "DON'T ENABLE UNTIL CYCLIC DEPENDENCIES ARE SOLVED" OFF)
option(USE_FETCH_GOOGLETEST "Fetch Google Test automatically" ON)
option(USE_FETCH_JSON "Fetch JSON automatically" ON)
option(USE_FETCH_SPDLOG "Fetch speed logger automatically" ON)
option(USE_FETCH_YAML "Fetch YAML automatically" ON)
option(USE_FETCH_SOCKET_IO "Fetch socket.io-client automatically" ON)
option(USE_FETCH_GLOG "Fetch glog automatically" ON)
option(USE_STACK_TRACE_LOGGER "Use a logger to trace the stack" OFF)
set(USE_BOW_FRAMEWORK "DBoW2" CACHE STRING "DBoW2 or FBoW")
set_property(CACHE USE_BOW_FRAMEWORK PROPERTY STRINGS "DBoW2" "FBoW")
set(USE_VIEWER "Pangolin" CACHE STRING "Pangoling or Socket publisher")
set_property(CACHE USE_VIEWER PROPERTY STRINGS "Pangolin" "Socket")

#Build in parallel in Visual Studio
add_compile_options($<$<CXX_COMPILER_ID:MSVC>:/MP>)

#Choose the BoW framework
if(USE_BOW_FRAMEWORK MATCHES "DBoW2")
    find_package(DBoW2 REQUIRED)
    set(BOW_INCLUDE_DIRS ${DBoW2_INCLUDE_DIRS} CACHE STRING "")
    set(BOW_LIBS ${DBoW2_LIBS} CACHE STRING "")
    add_definitions(-DUSE_DBOW2)
elseif(BOW_FRAMEWORK MATCHES "FBoW")
    find_package(fbow REQUIRED)
    set(BOW_INCLUDE_DIRS ${fbow_INCLUDE_DIRS} CACHE STRING "")
    set(BOW_LIBS ${fbow_LIBS} CACHE STRING "")
    remove_definitions(-DUSE_DBOW2)
else()
    message(FATAL_ERROR "Invalid BoW framework: ${USE_BOW_FRAMEWORK}")
endif()
message(STATUS "BOW include dirs: ${BOW_INCLUDE_DIRS}")
message(STATUS "BOW libs: ${BOW_LIBS}")

#Test that we can find rest of the required packages before going any further
find_package(OpenCV REQUIRED)
find_package(g2o REQUIRED)
find_package(Eigen3 REQUIRED)
if( USE_VIEWER STREQUAL "Pangolin" )
    find_package(Pangolin REQUIRED)
endif()

#Test that we find the required packages, depending on whether these
#are to be fetched by FetchContent
if( NOT USE_FETCH_GOOGLETEST )
    find_package(gtest REQUIRED)
endif()

if( NOT USE_FETCH_JSON )
    find_package(nlohmann-json REQUIRED)
endif()

if( NOT USE_FETCH_SPDLOG )
    find_package(spdlog REQUIRED)
endif()

if( NOT USE_FETCH_YAML )
    find_package(yaml-cpp REQUIRED)
endif()

if( NOT USE_FETCH_SOCKET_IO )
    find_package(socket.io-client REQUIRED)
endif()

if( NOT USE_FETCH_GLOG )
    find_package(glog REQUIRED)
endif()

#---------------
# Other options
#---------------
#Enable SSE3 for ORB extraction
option(USE_SSE_ORB "Enable SSE3 instruction for ORB extraction" OFF)
if(USE_SSE_ORB)
    include(CheckCXXCompilerFlag)
    CHECK_CXX_COMPILER_FLAG("-march=native" COMPILER_OPT_ARCH_NATIVE_SUPPORTED)
    if(COMPILER_OPT_ARCH_NATIVE_SUPPORTED)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -march=native")
        set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} -march=native")
        add_definitions(-DUSE_SSE_ORB)
        message(STATUS "SSE: enabled")
    else()
        CHECK_CXX_COMPILER_FLAG("/arch:AVX" COMPILER_OPT_ARCH_AVX_SUPPORTED)
        if(COMPILER_OPT_ARCH_AVX_SUPPORTED)
            set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /arch:AVX")
            set(CMAKE_C_FLAGS "${CMAKE_CXX_FLAGS} /arch:AVX")
            add_definitions(-DUSE_SSE_ORB)
            message(STATUS "SSE: enabled")
        endif()
    endif()
else()
    remove_definitions(-DUSE_SSE_ORB)
    #Fails in Linux if the flags are empty
    if(NOT ${CMAKE_CXX_FLAGS} STREQUAL "")
        string(REPLACE "/arch:AVX" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
    endif()
    if(NOT ${CMAKE_C_FLAGS} STREQUAL "")
        string(REPLACE "/arch:AVX" "" CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
    endif()
    if(NOT ${CMAKE_CXX_FLAGS} STREQUAL "")
        string(REPLACE "-march=native" "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
    endif()
    if(NOT ${CMAKE_C_FLAGS} STREQUAL "")
        string(REPLACE "-march=native" "" CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
    endif()
    message(STATUS "SSE: disabled")
endif()

#Enable OpenMP
option(USE_OPENMP "Enable OpenMP support" OFF)
find_package(OpenMP)
if(USE_OPENMP)
    if(OpenMP_FOUND)
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
        add_definitions(-DUSE_OPENMP)
        message(STATUS "OpenMP: ENABLED")
    else()
        remove_definitions(-DUSE_OPENMP)
        message(STATUS "OpenMP: DISABLED")
    endif()
else()
    remove_definitions(-DUSE_OPENMP)
    #Fails in Linux if the flags are empty
    if(NOT ${CMAKE_CXX_FLAGS} STREQUAL "")
        string(REPLACE ${OpenMP_CXX_FLAGS} "" CMAKE_CXX_FLAGS ${CMAKE_CXX_FLAGS})
    endif()
    if(NOT ${CMAKE_C_FLAGS} STREQUAL "")
        string(REPLACE ${OpenMP_C_FLAGS} "" CMAKE_C_FLAGS ${CMAKE_C_FLAGS})
    endif()
    message(STATUS "OpenMP: DISABLED")
endif()

#-----------
# Exporting
#-----------
set(OPENVSLAM_TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets" CACHE STRING "Name of the export target")
set(OPENVSLAM_NAMESPACE "${PROJECT_NAME}::" CACHE STRING "Name of the export target namespace")

#---------------------------------------
# Add the corresponding sub-directories
#---------------------------------------
add_subdirectory(3rdparty)
add_subdirectory(modules)
add_subdirectory(test)
add_subdirectory(examples)

#---------------------------
# Installation instructions
#---------------------------
include(GNUInstallDirs)
set(INSTALL_CONFIGDIR ${CMAKE_INSTALL_LIBDIR}/cmake/OpenVSLAM)

#Export the targets to a script
install(EXPORT ${OPENVSLAM_TARGETS_EXPORT_NAME}
  FILE
    ${OPENVSLAM_TARGETS_EXPORT_NAME}.cmake
  NAMESPACE
    ${OPENVSLAM_NAMESPACE}
  DESTINATION
    ${INSTALL_CONFIGDIR}
)

#Create a ConfigVersion.cmake file
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    ${CMAKE_CURRENT_BINARY_DIR}/OpenVSLAMConfigVersion.cmake
    VERSION ${PROJECT_VERSION}
    COMPATIBILITY AnyNewerVersion
)

#Create a configuration file
configure_package_config_file(
    ${CMAKE_SOURCE_DIR}/cmake/OpenVSLAMConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/OpenVSLAMConfig.cmake
    INSTALL_DESTINATION ${INSTALL_CONFIGDIR}
    PATH_VARS CMAKE_INSTALL_INCLUDEDIR CMAKE_INSTALL_LIBDIR
)

#Install the version- and configuration files
install(FILES
        ${CMAKE_CURRENT_BINARY_DIR}/OpenVSLAMConfig.cmake
        ${CMAKE_CURRENT_BINARY_DIR}/OpenVSLAMConfigVersion.cmake
        DESTINATION ${INSTALL_CONFIGDIR}
)

#export(PACKAGE OpenVSLAM)

