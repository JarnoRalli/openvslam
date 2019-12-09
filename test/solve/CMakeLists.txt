#########################
# Test essential solver
add_executable (test_essential_solver src/test_essential_solver.cpp)
target_link_libraries (test_essential_solver
    PRIVATE
        gtest_main
        type
        solve
        helper)
add_test (NAME test_essential_solver COMMAND test_essential_solver )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_essential_solver.cpp)
set_property( TARGET test_essential_solver PROPERTY FOLDER "tests/solver" )

#########################
# Test fundamental solver
add_executable (test_fundamental_solver src/test_fundamental_solver.cpp)
target_link_libraries (test_fundamental_solver
    PRIVATE
        gtest_main
        type
        solve
        helper)
add_test (NAME test_fundamental_solver COMMAND test_fundamental_solver )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_fundamental_solver.cpp)
set_property( TARGET test_fundamental_solver PROPERTY FOLDER "tests/solver" )

#########################
# Test homography solver
add_executable (test_homography_solver src/test_homography_solver.cpp)
target_link_libraries (test_homography_solver
    PRIVATE
        gtest_main
        type
        solve
        helper)
add_test (NAME test_homography_solver COMMAND test_homography_solver )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_homography_solver.cpp)
set_property( TARGET test_homography_solver PROPERTY FOLDER "tests/solver" )

#########################
# Test pnp solver
add_executable (test_pnp_solver src/test_pnp_solver.cpp)
target_link_libraries (test_pnp_solver
    PRIVATE
        gtest_main
        type
        solve
        helper)
add_test (NAME test_pnp_solver COMMAND test_pnp_solver )

# Project structure for Visual Studio
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR}/.. FILES src/test_pnp_solver.cpp)
set_property( TARGET test_pnp_solver PROPERTY FOLDER "tests/solver" )
