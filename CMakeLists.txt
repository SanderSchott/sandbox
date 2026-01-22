# Adapted from https://github.com/TheLartians/ModernCppStarter
cmake_minimum_required(VERSION 3.14)

# ---- Project ----
project(
  Sandbox
  VERSION 0.0.1
  LANGUAGES C CXX
)

set(CMAKE_CXX_STANDARD 23)
set(CPM_SOURCE_CACHE "build/.cache")

# ---- Add dependencies via CPM ----
# See https://github.com/TheLartians/CPM.cmake for more info
include(cmake/CPM.cmake)
CPMAddPackage("gh:onqtam/doctest@2.4.12") # Unit testing
CPMAddPackage("gh:TheLartians/Format.cmake@1.8.3") # format check action
CPMAddPackage("gh:jarro2783/cxxopts@3.3.1") # Command line options
CPMAddPackage("gh:sddale/csm-units@2.2.0")
CPMAddPackage(
  "gh:SanderSchott/gcem#main"
) # constexpr math library: forked from kthohr/gcem to fix cmake minimum version
  # issue

add_subdirectory(include/sandbox)
add_subdirectory(source/sandbox)
add_subdirectory(test/source)
