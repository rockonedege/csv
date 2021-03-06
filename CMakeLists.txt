cmake_minimum_required(VERSION 3.10)

project(csv LANGUAGES CXX VERSION 1.1.0)

option(CSV_BUILD_TESTS OFF)

# Find Threads

find_package(Threads REQUIRED)

# Add Library

add_library(${PROJECT_NAME} INTERFACE)
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})
target_include_directories(${PROJECT_NAME} INTERFACE
	$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
	$<INSTALL_INTERFACE:include>)
target_compile_features(${PROJECT_NAME} INTERFACE cxx_std_17)
target_link_libraries(${PROJECT_NAME} INTERFACE Threads::Threads)

# Build Tests

if(CSV_BUILD_TESTS)
	add_subdirectory("${CMAKE_CURRENT_SOURCE_DIR}/vendor/Catch2")
	enable_testing()
	macro(csv_add_test TESTNAME)
		add_executable(${TESTNAME} ${ARGN})
		target_link_libraries(${TESTNAME} PRIVATE ${PROJECT_NAME}::${PROJECT_NAME})
		target_link_libraries(${TESTNAME} PRIVATE Catch2)
		add_test(NAME ${TESTNAME} COMMAND ${TESTNAME})
		set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
	endmacro()
	add_subdirectory("${CMAKE_CURRENT_SOURCE_DIR}/tests")
endif()

# Install Library

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/
        DESTINATION include
        FILES_MATCHING PATTERN "*.hpp")

# CPack

set(CPACK_PACKAGE_VENDOR "Pranav Srinivas Kumar")
set(CPACK_PACKAGE_CONTACT "pranav.srinivas.kumar@gmail.com")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "CSV for Modern C++")
set(CPACK_PACKAGE_VERSION_MAJOR ${PROJECT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PROJECT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PROJECT_VERSION_PATCH})
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE")
set(CPACK_RESOURCE_FILE_README "${CMAKE_CURRENT_SOURCE_DIR}/README.md")

set(CPACK_SOURCE_GENERATOR "TGZ;ZIP")

include(CPack)
