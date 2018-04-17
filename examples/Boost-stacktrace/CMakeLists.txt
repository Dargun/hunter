# Copyright (c) 2013, Ruslan Baratov
# All rights reserved.

cmake_minimum_required(VERSION 3.0)

# Emulate HunterGate:
# * https://github.com/hunter-packages/gate
set(TESTING_CONFIG_OPT FILEPATH ${CMAKE_CURRENT_LIST_DIR}/config.cmake) 
include("../common.cmake")

project(download-boost-stacktrace)

hunter_add_package(Boost COMPONENTS system stacktrace)

if(MSVC)
  find_package(Boost CONFIG REQUIRED system stacktrace_windbg)
else()
  find_package(Boost CONFIG REQUIRED system stacktrace_backtrace)
endif()

add_executable(foo foo.cpp)
if(MSVC)
  target_link_libraries(foo PUBLIC Boost::system Boost::stacktrace_windbg ole32.lib dbgeng.lib)
else()
  target_link_libraries(foo PUBLIC Boost::system Boost::stacktrace_backtrace dl backtrace)
endif()
