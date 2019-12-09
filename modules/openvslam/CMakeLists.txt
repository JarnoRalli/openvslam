cmake_minimum_required(VERSION 3.12)
project(openvslam VERSION 2.0 LANGUAGES CXX C)

message(STATUS "\tmodule: ${PROJECT_NAME}")

##############################################
# Declare dependencies

##############################################
# Create target and set properties
list(   APPEND 
        src_files   
        src/system.cpp
        src/tracking_module.cpp
        src/mapping_module.cpp
        src/global_optimization_module.cpp)

list(   APPEND 
        header_files
        include/openvslam/openvslam/system.hpp
        include/openvslam/openvslam/tracking_module.hpp
        include/openvslam/openvslam/mapping_module.hpp
        include/openvslam/openvslam/global_optimization_module.hpp)

#Header files are added here so that they are shown properly in Visual Studio
add_library( ${PROJECT_NAME} ${src_files} ${header_files})

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
        feature
        camera
        type
        data
        module
        optimize
    PRIVATE
        io
        publisher
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
