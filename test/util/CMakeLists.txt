#########################
# Test fancy index
add_executable (test_fancy_index src/test_fancy_index.cpp)
target_link_libraries (test_fancy_index
    PRIVATE
        gtest_main
        util
        helper)
add_test (NAME test_fancy_index COMMAND test_fancy_index )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_fancy_index.cpp)
set_property( TARGET test_fancy_index PROPERTY FOLDER "tests/util" )

#########################
# Test random array
add_executable (test_random_array src/test_random_array.cpp)
target_link_libraries (test_random_array
    PRIVATE
        gtest_main
        util
        helper)
add_test (NAME test_random_array COMMAND test_random_array )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_random_array.cpp)
set_property( TARGET test_random_array PROPERTY FOLDER "tests/util" )

#########################
# Test trigonometric functions
add_executable (test_trigonometric src/test_trigonometric.cpp)
target_link_libraries (test_trigonometric
    PRIVATE
        gtest_main
        util
        helper)
add_test (NAME test_trigonometric COMMAND test_trigonometric )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_trigonometric.cpp)
set_property( TARGET test_trigonometric PROPERTY FOLDER "tests/util" )
