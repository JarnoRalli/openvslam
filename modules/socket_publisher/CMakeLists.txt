cmake_minimum_required(VERSION 3.12)
project(socket_publisher LANGUAGES CXX C)

message(STATUS "\tmodule: ${PROJECT_NAME}")

##############################################
# Declare dependencies

##############################################
# Generate Protobuf files automatically during CMake configure run.
# FetchContent installs both the protoc binary and the cpp-version.
# We try to generate the protobuf files automatically on the fly.
FetchContent_GetProperties(protobuf-exe protobuf-exe_SOURCE_DIR)
file(GLOB PROTOBUF_DEFINITION_FILES "protobuf/*.proto")
message(STATUS "\t\tprotobuf definition files: ${PROTOBUF_DEFINITION_FILES}")
set(PROTOBUF_INPUT_DIRECTORY "${PROJECT_SOURCE_DIR}")
set(PROTOBUF_OUTPUT_DIRECTORY "${PROJECT_SOURCE_DIR}")
set(PROTOBUF_HEADERS "")
set(PROTOBUF_SOURCES "")
foreach(file ${PROTOBUF_DEFINITION_FILES})
    get_filename_component(temp ${file} NAME_WE)
    list(APPEND PROTOBUF_HEADERS protobuf/${temp}.pb.h)
    list(APPEND PROTOBUF_SOURCES protobuf/${temp}.pb.cc)
    execute_process(
            COMMAND ./protoc --proto_path=${PROTOBUF_INPUT_DIRECTORY} --cpp_out=${PROTOBUF_OUTPUT_DIRECTORY} ${file}
            WORKING_DIRECTORY ${protobuf-exe_SOURCE_DIR}/bin
            RESULT_VARIABLE PROTOBUF_RESULT
            OUTPUT_VARIABLE PROTOBUF_OUTPUT_VARIABLE)
endforeach()
message(STATUS "\t\tgenerated protobuf headers: ${PROTOBUF_HEADERS}")
message(STATUS "\t\tgenerated protobuf sources: ${PROTOBUF_SOURCES}")

##############################################
# Create target and set properties
list(   APPEND 
        src_files
        src/publisher.cpp
        src/socket_client.cpp
        src/data_serializer.cpp
        )

list(   APPEND 
        header_files
        include/openvslam/socket_publisher/publisher.hpp
        include/openvslam/socket_publisher/socket_client.hpp
        include/openvslam/socket_publisher/data_serializer.hpp
        )

#Header files are added here so that they are shown properly in Visual Studio
add_library( ${PROJECT_NAME} ${src_files} ${header_files} ${PROTOBUF_HEADERS} ${PROTOBUF_SOURCES})

#We need to retrieve the installation folders
FetchContent_GetProperties(protobuf protobuf_SOURCE_DIR)
FetchContent_GetProperties(socket.io-client socket.io-client_SOURCE_DIR)

#Set target properties
target_include_directories(${PROJECT_NAME}
                PUBLIC
                    $<INSTALL_INTERFACE:include>
                    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
                    $<BUILD_INTERFACE:${socket.io-client_SOURCE_DIR}/src>
                    $<BUILD_INTERFACE:${protobuf_SOURCE_DIR}/src>
                PRIVATE
                    ${CMAKE_CURRENT_SOURCE_DIR}/src
                    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
                    )

target_compile_features(${PROJECT_NAME} PRIVATE cxx_auto_type)
target_compile_options(${PROJECT_NAME} PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall>)
target_compile_definitions(${PROJECT_NAME} PUBLIC _USE_MATH_DEFINES)

target_link_libraries(${PROJECT_NAME}
    PUBLIC
        config
        type
        sioclient
    PRIVATE
        openvslam
        publisher
        data
        spdlog::spdlog
        sioclient
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
