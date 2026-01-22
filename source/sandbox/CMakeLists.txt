# ---- Create Executable ----
add_executable(${PROJECT_NAME} main.cpp)

set_target_properties(${PROJECT_NAME} PROPERTIES LINKER_LANGUAGE CXX)
set_target_properties(${PROJECT_NAME} PROPERTIES CXX_STANDARD 23)

# Compiler Properties for executable
if(CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
  target_compile_options(
    ${PROJECT_NAME} PUBLIC /permissive- /O2 /bigobj /W4 /WX
  )
elseif(CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
  target_compile_options(
    ${PROJECT_NAME} PUBLIC -Wall -Wextra -Wpedantic -Werror -std=c++23
                           -Wno-psabi # disable abi change warnings
  )

  if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_options(${PROJECT_NAME} PUBLIC -g -O0)
  else()
    target_compile_options(${PROJECT_NAME} PUBLIC -O3)
  endif()
else()
  target_compile_options(
    ${PROJECT_NAME} PUBLIC -Wall -Wextra -Wpedantic -Werror -std=c++23
  )

  if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    target_compile_options(${PROJECT_NAME} PUBLIC -g -O0)
  else()
    target_compile_options(${PROJECT_NAME} PUBLIC -O3)
  endif()
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

if(CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
  set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lc++abi")
endif()
