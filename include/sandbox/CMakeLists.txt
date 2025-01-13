add_library(main_include INTERFACE example.hpp)

# add_subdirectory(subdirectory_name)

target_link_libraries(
  main_include INTERFACE CSMUnits::CSMUnits gcem
                         # subdirectory_library_name
)
