cmake_minimum_required(VERSION 3.8)
project(ros2_fundamentals_examples)
 
# Check if the compiler being used is GNU's C++ compiler (g++) or Clang.
# Add compiler flags for all targets that will be defined later in the
# CMakeLists file. These flags enable extra warnings to help catch
# potential issues in the code.
# Add options to the compilation process
if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()
 
# Locate and configure packages required by the project.
find_package(ament_cmake REQUIRED)
find_package(ament_cmake_python REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclpy REQUIRED)
find_package(std_msgs REQUIRED)
 
# Define a CMake variable named dependencies that lists all
# ROS 2 packages and other dependencies the project requires.
set(dependencies
  rclcpp
  std_msgs
)
 
# Add the specified directories to the list of paths that the compiler
# uses to search for header files. This is important for C++
# projects where you have custom header files that are not located
# in the standard system include paths.
include_directories(
  include
)
 
# Tells CMake to create an executable target named cpp_minimal_publisher
# from the source file src/cpp_minimal_publisher.cpp. Also make sure CMake
# knows about the program's dependencies.
add_executable(cpp_minimal_publisher src/cpp_minimal_publisher.cpp)
ament_target_dependencies(cpp_minimal_publisher ${dependencies})
 
add_executable(cpp_minimal_subscriber src/cpp_minimal_subscriber.cpp)
ament_target_dependencies(cpp_minimal_subscriber ${dependencies})
 
# Copy necessary files to designated locations in the project
install (
  DIRECTORY ros2_fundamentals_examples scripts
  DESTINATION share/${PROJECT_NAME}
)
 
install(
  DIRECTORY include/
  DESTINATION include
)
 
# Install cpp executables
install(
  TARGETS
  cpp_minimal_publisher
  cpp_minimal_subscriber
  DESTINATION lib/${PROJECT_NAME}
)
 
# Install Python modules for import
ament_python_install_package(${PROJECT_NAME})
 
# Add this section to install Python scripts
install(
  PROGRAMS
  ros2_fundamentals_examples/py_minimal_publisher.py
  ros2_fundamentals_examples/py_minimal_subscriber.py
  DESTINATION lib/${PROJECT_NAME}
)
 
# Automates the process of setting up linting for the package, which
# is the process of running tools that analyze the code for potential
# errors, style issues, and other discrepancies that do not adhere to
# specified coding standards or best practices.
if(BUILD_TESTING)
  # Find required test packages
  find_package(ament_lint_auto REQUIRED)
  find_package(ament_cmake_pytest REQUIRED)
  find_package(ament_cmake_gtest REQUIRED)
   
  # Python tests
  ament_add_pytest_test(minimal_publisher_test test/pytest/test_publisher.py
    TIMEOUT 60
  )
   
  # C++ tests
  ament_add_gtest(cpp_minimal_publisher_test
    test/gtest/test_publisher.cpp
  )
   
  # Add dependencies for the C++ test executable
  ament_target_dependencies(cpp_minimal_publisher_test
    ${dependencies}
  )
   
  # Optional: Enable other linting tools
  #set(ament_cmake_copyright_FOUND TRUE)
  #set(ament_cmake_cpplint_FOUND TRUE)
  #ament_lint_auto_find_test_dependencies()
endif()
 
# Used to export include directories of a package so that they can be easily
# included by other packages that depend on this package.
ament_export_include_directories(include)
 
# Generate and install all the necessary CMake and environment hooks that
# allow other packages to find and use this package.
ament_package()