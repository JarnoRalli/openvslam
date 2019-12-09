#########################
# Test angle checker
add_executable (test_angle_checker src/test_angle_checker.cpp)
target_link_libraries (test_angle_checker
    PRIVATE
        gtest_main
        match
        helper)
add_test (NAME test_angle_checker COMMAND test_angle_checker )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_angle_checker.cpp)
set_property( TARGET test_angle_checker PROPERTY FOLDER "tests/matcher" )

#########################
# Test base
add_executable (test_base src/test_base.cpp)
target_link_libraries (test_base
    PRIVATE
        gtest_main
        match
        helper)
add_test (NAME test_base COMMAND test_base )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_base.cpp)
set_property( TARGET test_base PROPERTY FOLDER "tests/matcher" )


