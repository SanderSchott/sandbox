# Adapted from https://github.com/TheLartians/ModernCppStarter
cmake_minimum_required(VERSION 3.14)

# ---- Project ----
project(
  Sandbox
  VERSION 0.0.1
  LANGUAGES C CXX
)

set(CMAKE_CXX_STANDARD 23)

# ---- Add dependencies via CPM ----
# See https://github.com/TheLartians/CPM.cmake for more info
include(cmake/CPM.cmake)
CPMAddPackage("gh:onqtam/doctest@2.4.11") # Unit testing
CPMAddPackage("gh:TheLartians/Format.cmake@1.7.3") # format check action
CPMAddPackage("gh:sddale/csm-units#main")

# Source and header globs
file(GLOB_RECURSE sources CONFIGURE_DEPENDS
     "${CMAKE_CURRENT_SOURCE_DIR}/source/sandbox/*.cpp"
)
file(GLOB_RECURSE headers CONFIGURE_DEPENDS
     "${CMAKE_CURRENT_SOURCE_DIR}/include/sandbox/*.hpp"
)

# ---- Create Executable ----
add_executable(${PROJECT_NAME} ${headers} ${sources})
set_target_properties(${PROJECT_NAME} PROPERTIES LINKER_LANGUAGE CXX)
set_target_properties(${PROJECT_NAME} PROPERTIES CXX_STANDARD 23)

# Compiler Properties for executable

if(MSVC)
  target_compile_options(
    ${PROJECT_NAME} PUBLIC /permissive- /O2 /bigobj /W4 /WX
  )
else()
  target_compile_options(
    ${PROJECT_NAME} PUBLIC -O3 -Wall -Wextra -Wpedantic -Werror -std=c++2b
  )
endif()

if(MINGW)
  target_link_options(
    ${PROJECT_NAME} PUBLIC -static-libgcc -static-libstdc++ -static
  )
endif()

target_compile_features(${PROJECT_NAME} PRIVATE cxx_std_23)

target_include_directories(
  ${PROJECT_NAME}
  PUBLIC $<BUILD_INTERFACE:${PROJECT_SOURCE_DIR}/include>
         $<INSTALL_INTERFACE:include/${PROJECT_NAME}-${PROJECT_VERSION}>
)

target_link_libraries(${PROJECT_NAME} PRIVATE CSMUnits::CSMUnits)

# ---- Test Executable ----
# test file glob
file(GLOB_RECURSE test_sources CONFIGURE_DEPENDS
     "${CMAKE_CURRENT_SOURCE_DIR}/test/source/*.cpp"
)

# Test Executable
add_executable(${PROJECT_NAME}_test ${test_sources})
set_target_properties(${PROJECT_NAME}_test PROPERTIES LINKER_LANGUAGE CXX)
set_target_properties(${PROJECT_NAME}_test PROPERTIES CXX_STANDARD 23)

target_link_libraries(
  ${PROJECT_NAME}_test PRIVATE doctest::doctest CSMUnits::CSMUnits
)

target_include_directories(${PROJECT_NAME}_test PRIVATE "test" "include" ".")

set_property(
  TARGET ${PROJECT_NAME}_test # PROPERTY CMAKE_BUILD_TYPE Debug
  PROPERTY CMAKE_EXPORT_COMPILE_COMMANDS ON
)

# Compiler Properties for testing
target_compile_features(${PROJECT_NAME}_test PRIVATE cxx_std_23)
if(MSVC)
  target_compile_options(${PROJECT_NAME}_test PRIVATE /bigobj /W4 /WX)
else()
  target_compile_options(
    ${PROJECT_NAME}_test PRIVATE -g -Wall -Wextra -Wpedantic -Werror -std=c++2b
  )
endif()

enable_testing()
include(${doctest_SOURCE_DIR}/scripts/cmake/doctest.cmake)
doctest_discover_tests(${PROJECT_NAME}_test)
