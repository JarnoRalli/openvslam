cmake_minimum_required(VERSION 3.12)
project(type LANGUAGES CXX C)

message(STATUS "\tmodule: ${PROJECT_NAME}")

##############################################
# Declare dependencies
find_package(Eigen3 REQUIRED NO_MODULE)
find_package(OpenCV REQUIRED COMPONENTS core)

##############################################
# Create target and set properties

list(   APPEND 
        header_files
        include/openvslam/type/type.hpp )

add_library( ${PROJECT_NAME} INTERFACE )

#Set target properties
target_include_directories( ${PROJECT_NAME}
                            INTERFACE
                                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                                $<INSTALL_INTERFACE:include>
)
                                
target_link_libraries(${PROJECT_NAME} INTERFACE Eigen3::Eigen opencv_core)

source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${header_files})

#############################
# Installation instructions
install(TARGETS ${PROJECT_NAME}
    EXPORT ${OPENVSLAM_TARGETS_EXPORT_NAME}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

install(DIRECTORY include DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
