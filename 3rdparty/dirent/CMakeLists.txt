cmake_minimum_required(VERSION 3.10)
project(dirent LANGUAGES CXX C)

##############################################
# Declare dependencies

##############################################
# Create target and set properties

list(   APPEND 
        header_files
        include/dirent.h )

add_library( ${PROJECT_NAME} INTERFACE )

#Set target properties
target_include_directories( ${PROJECT_NAME}
                            INTERFACE
                                $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                                $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>)

source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${header_files})