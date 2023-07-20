cmake_minimum_required(VERSION 3.20)
project(proj LANGUAGES C CXX)

# cmake modules
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/clang-cmake/cmake)
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)



list(APPEND IWYU_IMP ${PROJECT_SOURCE_DIR}/gtest.imp)



# standards
# GoogleTest requires at least C++14
set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# includes
include(SetupGTest)
include(SetupProjectOptions)
include(CTest)
include(ClangCmake)


project_options(
      PREFIX "proj"
      ENABLE_CACHE
      #${ENABLE_CPPCHECK}
      #ENABLE_CLANG_TIDY
      #ENABLE_VS_ANALYSIS
      # ENABLE_CONAN
      # ENABLE_INTERPROCEDURAL_OPTIMIZATION
      # ENABLE_NATIVE_OPTIMIZATION
      #${ENABLE_DOXYGEN}
      #${ENABLE_COVERAGE}
      #${ENABLE_SANITIZER_ADDRESS}
      #${ENABLE_SANITIZER_UNDEFINED_BEHAVIOR}
      # ENABLE_SANITIZER_THREAD
      # ENABLE_SANITIZER_MEMORY
      # ENABLE_CONTROL_FLOW_PROTECTION
      # ENABLE_STACK_PROTECTION
      # ENABLE_OVERFLOW_PROTECTION
      # ENABLE_ELF_PROTECTION
      # ENABLE_RUNTIME_SYMBOLS_RESOLUTION
      ENABLE_COMPILE_COMMANDS_SYMLINK
      # ENABLE_PCH
      # PCH_HEADERS
      WARNINGS_AS_ERRORS
      # ENABLE_INCLUDE_WHAT_YOU_USE
      # ENABLE_GCC_ANALYZER
      # ENABLE_BUILD_WITH_TIME_TRACE
      # ENABLE_UNITY
      # LINKER "lld"
      # CONAN_PROFILE ${profile_path}  # passes a profile to conan: see https://docs.conan.io/en/latest/reference/profiles.html
)


# Static library
file(GLOB SOURCES ${PROJECT_SOURCE_DIR}/libproj/*.?pp)
list(FILTER SOURCES EXCLUDE REGEX ".test.cpp")
add_library(LibProj STATIC ${SOURCES})
target_include_directories(LibProj PUBLIC ${CMAKE_SOURCE_DIR})


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
