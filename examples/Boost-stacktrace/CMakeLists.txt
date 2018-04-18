# Copyright (c) 2013, Ruslan Baratov
# All rights reserved.

cmake_minimum_required(VERSION 3.0)

# Emulate HunterGate:
# * https://github.com/hunter-packages/gate
set(TESTING_CONFIG_OPT FILEPATH ${CMAKE_CURRENT_LIST_DIR}/config.cmake) 
include("../common.cmake")

project(download-boost-stacktrace)

hunter_add_package(Boost COMPONENTS system stacktrace)

find_package(Boost CONFIG REQUIRED system)
add_executable(foo foo.cpp)

if(MSVC)
  find_package(Boost CONFIG stacktrace_windbg)
  if(stacktrace_windbg_FOUND)
    target_link_libraries(foo PUBLIC Boost::system Boost::stacktrace_windbg ole32.lib dbgeng.lib)
  else()
    message(WARNING "Boost stacktrace windbg not found, use only symlinks!")
  endif()
else()
  find_package(Boost CONFIG stacktrace_backtrace stacktrace_addr2line)
  if(stacktrace_backtrace_FOUND)
    target_link_libraries(foo PUBLIC Boost::system Boost::stacktrace_backtrace dl backtrace)
  elseif(stacktrace_addr2line_FOUND)
    target_link_libraries(foo PUBLIC Boost::system Boost::stacktrace_addr2line dl)
  else()
    message(WARNING "Boost stacktrace backtrace or addr2line not found, use only symlinks!")
  endif()
endif()
