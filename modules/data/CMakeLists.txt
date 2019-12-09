cmake_minimum_required(VERSION 3.12)
project(data LANGUAGES CXX C)

message(STATUS "\tmodule: ${PROJECT_NAME}")

##############################################
# Declare dependencies
find_package(OpenCV REQUIRED COMPONENTS core)

##############################################
# Create target and set properties
list(   APPEND 
        src_files
        src/common.cpp
        src/frame.cpp
        src/keyframe.cpp
        src/landmark.cpp
        src/graph_node.cpp
        src/camera_database.cpp
        src/map_database.cpp
        src/bow_database.cpp
        src/frame_statistics.cpp)
        
list(   APPEND 
        header_files   
        include/openvslam/data/common.hpp
        include/openvslam/data/frame.hpp
        include/openvslam/data/keyframe.hpp
        include/openvslam/data/landmark.hpp
        include/openvslam/data/graph_node.hpp
        include/openvslam/data/camera_database.hpp
        include/openvslam/data/map_database.hpp
        include/openvslam/data/bow_database.hpp
        include/openvslam/data//frame_statistics.hpp)

#Header files are added here so that they are shown properly in Visual Studio
add_library( ${PROJECT_NAME} ${src_files} ${header_files})

#Set target properties
target_include_directories(${PROJECT_NAME}
                PUBLIC
                    $<INSTALL_INTERFACE:include>
                    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                    $<BUILD_INTERFACE:${BOW_INCLUDE_DIRS}> #DBoW2 project does not export targets properly
                PRIVATE
                    ${CMAKE_CURRENT_SOURCE_DIR}/src
)

target_compile_features(${PROJECT_NAME} PRIVATE cxx_auto_type)
target_compile_options(${PROJECT_NAME} PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall>)
target_compile_definitions(${PROJECT_NAME} PUBLIC _USE_MATH_DEFINES)

target_link_libraries(${PROJECT_NAME}
    PUBLIC
        opencv_core
        ${BOW_LIBS}
        nlohmann_json
        camera
        type
        feature
        util
        match
    PRIVATE
        spdlog::spdlog
)

######################################
# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES ${src_files} ${header_files})
set_property( TARGET ${PROJECT_NAME} PROPERTY FOLDER "modules" )

#############################
# Installation instructions
install(TARGETS ${PROJECT_NAME}
    EXPORT ${OPENVSLAM_TARGETS_EXPORT_NAME}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(DIRECTORY include DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
