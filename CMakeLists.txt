cmake_minimum_required(VERSION 3.16)
project(proj)

# cmake modules
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/clang-cmake/cmake)
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)




# standards
# GoogleTest requires at least C++14
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# includes
include(SetupGTest)
include(ClangCmake)



# Static library
file(GLOB SOURCES ${PROJECT_SOURCE_DIR}/libproj/*.?pp)
list(FILTER SOURCES EXCLUDE REGEX ".test.cpp")
add_library(LibProj STATIC ${SOURCES})


file(GLOB SOURCES ${PROJECT_SOURCE_DIR}/proj/*.?pp)
add_executable(Proj ${SOURCES})
target_link_libraries(Proj PUBLIC LibProj)


enable_testing()

# testing
file(GLOB TEST_SOURCES libproj/*.test.cpp)
add_executable(ProjTest ${TEST_SOURCES})
target_link_libraries(ProjTest
    PRIVATE LibProj
    PRIVATE GTest::gtest_main
)

include(GoogleTest)
gtest_discover_tests(ProjTest)

target_setup(LibProj)
target_setup(Proj)
target_setup(ProjTest)