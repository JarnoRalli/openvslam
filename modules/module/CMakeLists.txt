cmake_minimum_required(VERSION 3.12)
project(module LANGUAGES CXX C)

message(STATUS "\tmodule: ${PROJECT_NAME}")

##############################################
# Declare dependencies
find_package(g2o REQUIRED COMPONENTS
                            g2o::core
                            g2o::stuff
                            g2o::solver_dense
                            g2o::solver_eigen
                            g2o::types_sba
                            g2o::types_sim3)

##############################################
# Create target and set properties
list(   APPEND 
        src_files   
        src/initializer.cpp
        src/relocalizer.cpp
        src/frame_tracker.cpp
        src/keyframe_inserter.cpp
        src/two_view_triangulator.cpp
        src/local_map_cleaner.cpp
        src/loop_detector.cpp
        src/loop_bundle_adjuster.cpp)

list(   APPEND 
        header_files
        include/openvslam/module/type.hpp
        include/openvslam/module/initializer.hpp
        include/openvslam/module/relocalizer.hpp
        include/openvslam/module/frame_tracker.hpp
        include/openvslam/module/keyframe_inserter.hpp
        include/openvslam/module/two_view_triangulator.hpp
        include/openvslam/module/local_map_cleaner.hpp
        include/openvslam/module/loop_detector.hpp
        include/openvslam/module/loop_bundle_adjuster.hpp
)

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
        optimize
        data
        initialize
        camera
        match
        solve
        type
    PRIVATE
        config
        util
        openvslam
        spdlog::spdlog
        g2o::core
        g2o::stuff
        g2o::solver_dense
        g2o::solver_eigen
        g2o::types_sba
        g2o::types_sim3)

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
