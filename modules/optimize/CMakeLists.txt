cmake_minimum_required(VERSION 3.12)
project(optimize LANGUAGES CXX C)

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
        src/pose_optimizer.cpp
        src/local_bundle_adjuster.cpp
        src/transform_optimizer.cpp
        src/graph_optimizer.cpp
        src/global_bundle_adjuster.cpp
        src/g2o/landmark_vertex.cpp
        src/g2o/landmark_vertex_container.cpp
        src/g2o/se3/shot_vertex.cpp
        src/g2o/se3/shot_vertex_container.cpp
        src/g2o/se3/perspective_pose_opt_edge.cpp
        src/g2o/se3/perspective_reproj_edge.cpp
        src/g2o/se3/equirectangular_pose_opt_edge.cpp
        src/g2o/se3/equirectangular_reproj_edge.cpp
        src/g2o/sim3/shot_vertex.cpp
        src/g2o/sim3/graph_opt_edge.cpp
        src/g2o/sim3/transform_vertex.cpp
        src/g2o/sim3/backward_reproj_edge.cpp
        src/g2o/sim3/forward_reproj_edge.cpp)

list(   APPEND 
        header_files
        include/openvslam/optimize/pose_optimizer.hpp
        include/openvslam/optimize/local_bundle_adjuster.hpp
        include/openvslam/optimize/transform_optimizer.hpp
        include/openvslam/optimize/graph_optimizer.hpp
        include/openvslam/optimize/global_bundle_adjuster.hpp
        include/openvslam/optimize/g2o/landmark_vertex.hpp
        include/openvslam/optimize/g2o/landmark_vertex_container.hpp
        include/openvslam/optimize/g2o/se3/shot_vertex.hpp
        include/openvslam/optimize/g2o/se3/shot_vertex_container.hpp
        include/openvslam/optimize/g2o/se3/perspective_pose_opt_edge.hpp
        include/openvslam/optimize/g2o/se3/perspective_reproj_edge.hpp
        include/openvslam/optimize/g2o/se3/equirectangular_pose_opt_edge.hpp
        include/openvslam/optimize/g2o/se3/equirectangular_reproj_edge.hpp
        include/openvslam/optimize/g2o/sim3/shot_vertex.hpp
        include/openvslam/optimize/g2o/sim3/graph_opt_edge.hpp
        include/openvslam/optimize/g2o/sim3/transform_vertex.hpp
        include/openvslam/optimize/g2o/sim3/backward_reproj_edge.hpp
        include/openvslam/optimize/g2o/sim3/forward_reproj_edge.hpp
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
        type
        module
    PRIVATE
        data
        camera
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
