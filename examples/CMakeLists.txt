cmake_minimum_required(VERSION 3.12)
project(examples VERSION 2.0 LANGUAGES CXX C)
find_package(OpenCV REQUIRED COMPONENTS core highgui imgproc imgcodecs videoio)

message(STATUS "--------")
message(STATUS "EXAMPLES")
message(STATUS "--------")

if(USE_VIEWER STREQUAL "Pangolin")
    message(STATUS "\tViewer for examples: pangolin viewer")
elseif(USE_VIEWER STREQUAL "Socket")
    message(STATUS "\tViewer for examples: socket publisher")
else()
    message(STATUS "\tViewer for examples: none")
endif()

include_directories(include)
set(EXECUTABLE_TARGETS "")

# Run camera slam
message(STATUS "\trun_camera_slam")
add_executable(run_camera_slam ./src/run_camera_slam.cc)
target_link_libraries(run_camera_slam PRIVATE openvslam util popl)
set_property( TARGET run_camera_slam PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_camera_slam)

# Run camera localization
message(STATUS "\trun_camera_localization")
add_executable(run_camera_localization ./src/run_camera_localization.cc)
target_link_libraries(run_camera_localization PRIVATE openvslam popl)
set_property( TARGET run_camera_localization PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_camera_localization)

# Run image slam
message(STATUS "\trun_image_slam")
add_executable(run_image_slam ./src/run_image_slam.cc ./src/util/image_util.cc)
target_link_libraries(run_image_slam PRIVATE openvslam popl)
set_property( TARGET run_image_slam PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_image_slam)

# Run image localization
message(STATUS "\trun_image_localization")
add_executable(run_image_localization ./src/run_image_localization.cc ./src/util/image_util.cc)
target_link_libraries (run_image_localization PRIVATE openvslam popl)
set_property( TARGET run_camera_localization PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_image_localization)

# Run video slam
message(STATUS "\trun_video_slam")
add_executable(run_video_slam ./src/run_video_slam.cc)
target_link_libraries (run_video_slam PRIVATE openvslam popl)
set_property( TARGET run_video_slam PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_video_slam)

# Run video localization
message(STATUS "\trun_video_localization")
add_executable(run_video_localization ./src/run_video_localization.cc)
target_link_libraries (run_video_localization PRIVATE openvslam popl)
set_property( TARGET run_video_localization PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_video_localization)

# Run euroc slam
message(STATUS "\trun_euroc_slam")
add_executable(run_euroc_slam ./src/run_euroc_slam.cc ./src/util/euroc_util.cc)
target_link_libraries (run_euroc_slam PRIVATE openvslam util popl)
set_property( TARGET run_euroc_slam PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_euroc_slam)

# Run kitti slam
message(STATUS "\trun_kitti_slam")
add_executable(run_kitti_slam ./src/run_kitti_slam.cc ./src/util/kitti_util.cc)
target_link_libraries (run_kitti_slam PRIVATE openvslam popl)
set_property( TARGET run_kitti_slam PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_kitti_slam)

# Run tum rgbd slam
message(STATUS "\trun_tum_rgbd_slam")
add_executable(run_tum_rgbd_slam ./src/run_tum_rgbd_slam.cc ./src/util/tum_rgbd_util.cc)
target_link_libraries (run_tum_rgbd_slam PRIVATE openvslam popl)
set_property( TARGET run_tum_rgbd_slam PROPERTY FOLDER "examples" )
list(APPEND EXECUTABLE_TARGETS run_tum_rgbd_slam)

foreach(EXECUTABLE_TARGET IN LISTS EXECUTABLE_TARGETS)
    # set output directory for executables
    set_target_properties(${EXECUTABLE_TARGET} PROPERTIES
            RUNTIME_OUTPUT_DIRECTORY_DEBUG "${CMAKE_BINARY_DIR}/bin"
            RUNTIME_OUTPUT_DIRECTORY_RELEASE "${CMAKE_BINARY_DIR}/bin"
            RUNTIME_OUTPUT_DIRECTORY_MINSIZEREL "${CMAKE_BINARY_DIR}/bin"
            RUNTIME_OUTPUT_DIRECTORY_RELWITHDEBINFO "${CMAKE_BINARY_DIR}/bin")
            
    # Link against OpenCV
    target_link_libraries(${EXECUTABLE_TARGET} PRIVATE opencv_core opencv_imgproc opencv_imgcodecs opencv_videoio opencv_calib3d)

    # pangolin_viewer is used on a priority basis
    if(USE_VIEWER STREQUAL "Pangolin")
        # link viewer
        target_link_libraries(${EXECUTABLE_TARGET} PRIVATE pangolin_viewer)
        target_compile_definitions(${EXECUTABLE_TARGET} PRIVATE USE_PANGOLIN_VIEWER)
    elseif(USE_VIEWER STREQUAL "Socket")
        # link viewer
        target_link_libraries(${EXECUTABLE_TARGET} PRIVATE socket_publisher)
        target_compile_definitions(${EXECUTABLE_TARGET} PRIVATE USE_OSCKET_PUBLISHER)
    endif()
    
    # For Windows we use a ported version of the dirent
    if(WIN32)
        target_link_libraries(${EXECUTABLE_TARGET} PRIVATE dirent)
    endif(WIN32)

     # setup stack trace logger
     if(USE_STACK_TRACE_LOGGER)
         target_compile_definitions(${EXECUTABLE_TARGET}
                 PRIVATE -DUSE_STACK_TRACE_LOGGER)
         target_link_libraries(${EXECUTABLE_TARGET} PRIVATE
                 glog::glog)
     endif()

    # # setup google-perftools
    # if(USE_GOOGLE_PERFTOOLS)
        # target_compile_definitions(${EXECUTABLE_TARGET}
                # PRIVATE -DUSE_GOOGLE_PERFTOOLS)
        # target_link_libraries(${EXECUTABLE_TARGET} PRIVATE
                # ${GPERFTOOLS_LIBRARIES})
    # endif()

    # Installation
    install(TARGETS ${EXECUTABLE_TARGET}
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

endforeach()
