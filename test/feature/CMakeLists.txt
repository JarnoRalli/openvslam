##############################################
# Declare dependencies
find_package(OpenCV REQUIRED COMPONENTS highgui)

#########################
# Test orb extractor
add_executable (test_orb_extractor src/test_orb_extractor.cpp)
target_link_libraries (test_orb_extractor
    PRIVATE
        gtest_main
        feature
        helper
        opencv_highgui
)
target_compile_definitions(test_orb_extractor PRIVATE TEST_DATA_DIR="${CMAKE_CURRENT_SOURCE_DIR}/../data/")
add_test (NAME test_orb_extractor COMMAND test_orb_extractor )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_orb_extractor.cpp)
set_property( TARGET test_orb_extractor PROPERTY FOLDER "tests/features" )

#########################
# Test orb params
add_executable (test_orb_params src/test_orb_params.cpp)
target_link_libraries (test_orb_params
    PRIVATE
        gtest_main
        feature
        helper
        opencv_highgui
)
add_test (NAME test_orb_params COMMAND test_orb_params )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_orb_params.cpp)
set_property( TARGET test_orb_params PROPERTY FOLDER "tests/features" )




