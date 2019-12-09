cmake_minimum_required(VERSION 3.10)
project(helper LANGUAGES CXX C)

##############################################
# Declare dependencies

##############################################
# Create target and set properties
list(   APPEND 
        src_files   
        src/bearing_vector.cc
        src/keypoint.cc
        src/landmark.cc)

list(   APPEND 
        header_files
        include/openvslam/helper/bearing_vector.h
        include/openvslam/helper/keypoint.h
        include/openvslam/helper/landmark.h)

#Header files are added here so that they are shown properly in Visual Studio
add_library( ${PROJECT_NAME} STATIC ${src_files} ${header_files})

#Set target properties
target_include_directories(${PROJECT_NAME}
                PUBLIC
                    $<INSTALL_INTERFACE:include>
                    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                PRIVATE
                    ${CMAKE_CURRENT_SOURCE_DIR}/src)

target_compile_features(${PROJECT_NAME} PRIVATE cxx_auto_type)
target_compile_options(${PROJECT_NAME} PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall>)
target_compile_definitions(${PROJECT_NAME} PUBLIC _USE_MATH_DEFINES)

target_link_libraries(${PROJECT_NAME}
    PUBLIC
        type
)

######################################
# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES ${src_files} ${header_files})
set_property( TARGET ${PROJECT_NAME} PROPERTY FOLDER "tests/helper" )

