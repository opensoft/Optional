# if CMAKE_VERSION >= 3.0 this project installs an INTERFACE target for
# the optional.hpp file.
#
# Usage:
#
# In your project's CMakeLists.txt:
#
#     find_package(akrzemi1_optional REQUIRED)
#     ...
#     target_link_libraries(mytarget ... akrzemi1::optional ...)
#
# In your C++ source file:
#
#     #include "3rdparty/optional/optional.hpp"
#

cmake_minimum_required(VERSION 3.12.0)
project(optional)
enable_testing()

add_library(optional INTERFACE)
target_include_directories(optional INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:include>)
install(TARGETS optional EXPORT optional-targets)
install(EXPORT optional-targets DESTINATION lib/cmake/3rdparty
    FILE akrzemi1_optional-config.cmake
    NAMESPACE akrzemi1::)
install(FILES optional.hpp DESTINATION include/3rdparty/optional)
export (TARGETS optional FILE akrzemi1_optional-config.cmake)

if(MSVC)
    set(OPTIONAL_TEST_COMPILER_FLAGS "-W4 -wd4100")
else()
    set(OPTIONAL_TEST_COMPILER_FLAGS "-Wall -Wextra -Wno-unused-variable")
endif()

if (NOT ANDROID)
    add_executable(test_optional test_optional.cpp)
    set_target_properties(test_optional PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
        CXX_EXTENSIONS OFF
        COMPILE_FLAGS ${OPTIONAL_TEST_COMPILER_FLAGS}
    )
    add_executable(test_type_traits test_type_traits.cpp)
    set_target_properties(test_type_traits PROPERTIES
        CXX_STANDARD 14
        CXX_STANDARD_REQUIRED ON
        CXX_EXTENSIONS OFF
        COMPILE_FLAGS ${OPTIONAL_TEST_COMPILER_FLAGS}
    )

    add_test(test_optional test_optional)
    add_test(test_type_traits test_type_traits)
endif()
