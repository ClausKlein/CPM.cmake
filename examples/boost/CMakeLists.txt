cmake_minimum_required(VERSION 3.14...3.26)

project(CPMExampleBoost)

# ---- Create binary ----

add_executable(CPMExampleAsio deferred.cpp)
target_compile_features(CPMExampleAsio PRIVATE cxx_std_17)

add_executable(CPMExampleRegex regex.cpp)
target_compile_features(CPMExampleRegex PRIVATE cxx_std_17)

add_executable(CPMExampleTest const_string_test.cpp)
target_compile_features(CPMExampleTest PRIVATE cxx_std_17)

# ---- Dependencies ----

include(../../cmake/CPM.cmake)

option(BUILD_TESTING "Enable unit tests" ON)
if(BUILD_TESTING)
  enable_testing()
endif()

set(BOOST_INCLUDE_LIBRARIES
    asio
    atomic
    chrono
    container
    coroutine
    context
    datetime
    exception
    filesystem
    locale
    test
    thread
    regex
    system
)
CPMAddPackage(
  NAME Boost
  VERSION 1.81.0
  GITHUB_REPOSITORY "boostorg/boost"
  GIT_TAG "boost-1.81.0"
)
if(Boost_ADDED)
  set(FETCHCONTENT_SOURCE_DIR_BOOST ${Boost_SOURCE_DIR})
  list(APPEND CMAKE_MODULE_PATH ${Boost_SOURCE_DIR}/tools/cmake/include)
  add_subdirectory(${Boost_SOURCE_DIR}/libs/locale/test locale_test)
endif()

target_link_libraries(CPMExampleAsio PRIVATE Boost::asio)
target_link_libraries(CPMExampleRegex PRIVATE Boost::regex)
target_link_libraries(CPMExampleTest PRIVATE Boost::unit_test_framework)

# ---- Testing ----

enable_testing()

add_test(NAME CPMExampleAsio COMMAND CPMExampleAsio)
add_test(NAME CPMExampleRegex COMMAND CPMExampleAsio)
add_test(NAME CPMExampleTest COMMAND CPMExampleTest)
