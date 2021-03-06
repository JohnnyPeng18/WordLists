cmake_minimum_required(VERSION 2.8.2)

project(WordLists-UnitTest LANGUAGES CXX)

set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        PROPERTY EP_UPDATE_DISCONNECTED 1)
if(CMAKE_BUILD_TYPE STREQUAL "Coverage")
  set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -O0 --coverage -fprofile-arcs -ftest-coverage")
endif()


# Download and unpack googletest at configure time
configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "CMake step for googletest failed: ${result}")
endif()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_CURRENT_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "Build step for googletest failed: ${result}")
endif()

# Prevent overriding the parent project's compiler/linker
# settings on Windows
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Add googletest directly to our build. This defines
# the gtest and gtest_main targets.
add_subdirectory(${CMAKE_CURRENT_BINARY_DIR}/googletest-src
                 ${CMAKE_CURRENT_BINARY_DIR}/googletest-build
                 EXCLUDE_FROM_ALL)

# The gtest/gtest_main targets carry header search path
# dependencies automatically when using CMake 2.8.11 or
# later. Otherwise we have to add them here ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
  include_directories("${gtest_SOURCE_DIR}/include")
endif()

add_subdirectory("../src" "WordLists-build")
include_directories("../src")

enable_testing()
# Now simply link against gtest or gtest_main as needed. Eg

add_executable(io_test io_test.cpp)
target_link_libraries(io_test PRIVATE gtest_main IO)
add_test(NAME io_test COMMAND io_test)

add_executable(interface_test interface_test.cpp)
target_link_libraries(interface_test PRIVATE gtest_main interface)
add_test(NAME interface_test COMMAND interface_test)

add_executable(text_process_test text_process_test.cpp)
target_link_libraries(text_process_test PRIVATE gtest_main interface)
add_test(NAME text_process_test COMMAND text_process_test)

add_executable(get_words_exception_test get_words_exception_test.cpp)
target_link_libraries(get_words_exception_test PRIVATE gtest_main interface)
add_test(NAME get_words_exception_test COMMAND text_process_test)