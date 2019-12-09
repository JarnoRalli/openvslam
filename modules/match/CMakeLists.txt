cmake_minimum_required(VERSION 3.12)
project(match LANGUAGES CXX C)

message(STATUS "\tmodule: ${PROJECT_NAME}")

##############################################
# Declare dependencies

##############################################
# Create target and set properties
list(   APPEND 
        src_files   
        src/area.cpp
        src/bow_tree.cpp
        src/fuse.cpp
        src/projection.cpp
        src/robust.cpp
        src/stereo.cpp)
        
list(   APPEND 
        header_files   
        include/openvslam/match/area.hpp
        include/openvslam/match/bow_tree.hpp
        include/openvslam/match/fuse.hpp
        include/openvslam/match/projection.hpp
        include/openvslam/match/robust.hpp
        include/openvslam/match/stereo.hpp)

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
target_compile_definitions(${PROJECT_NAME} PUBLIC USE_DBOW2)

target_link_libraries(${PROJECT_NAME}
    PUBLIC
        type
    PRIVATE
        data
        camera
        solve
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
