cmake_minimum_required(VERSION 3.18.1)
project("lsplant_test")

set(CMAKE_CXX_STANDARD 23)

if(EXISTS "${CMAKE_SOURCE_DIR}/external/lsparself")
    add_subdirectory(external/lsparself)
    set(USE_LSPARSELF ON)
else()
    set(USE_LSPARSELF OFF)
endif()
add_library(test SHARED test.cpp)
set_target_properties(test PROPERTIES CXX_SCAN_FOR_MODULES ON)
if(USE_LSPARSELF)
    target_compile_definitions(test PRIVATE USE_LSPARSELF)
endif()
find_package(dobby REQUIRED CONFIG)
find_package(lsplant REQUIRED CONFIG)

get_property(lsplant_INCLUDE TARGET lsplant::lsplant PROPERTY INTERFACE_INCLUDE_DIRECTORIES)
file(GLOB_RECURSE lsplant_MODULES "${lsplant_INCLUDE}/*.ixx")
target_sources(lsplant::lsplant INTERFACE FILE_SET CXX_MODULES BASE_DIRS ${lsplant_INCLUDE} FILES ${lsplant_MODULES})
set_target_properties(lsplant::lsplant PROPERTIES IMPORTED_CXX_MODULES_COMPILE_FEATURES "cxx_std_23")
set_target_properties(lsplant::lsplant PROPERTIES IMPORTED_CXX_MODULES_INCLUDE_DIRECTORIES "${lsplant_INCLUDE}")

target_link_libraries(test log dobby::dobby lsplant::lsplant)
if(USE_LSPARSELF)
    target_link_libraries(test lsparself)
endif()
