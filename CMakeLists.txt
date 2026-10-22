cmake_minimum_required(VERSION 3.19)
project(ipoke~)

if(NOT SDK)
  message(FATAL_ERROR "Pass -DSDK=/path/to/max-sdk-base")
endif()

set(C74_LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/out")

include(${SDK}/script/max-pretarget.cmake)
include_directories(${C74_INCLUDES})

add_library(${PROJECT_NAME} MODULE ipoke~.c)

include(${SDK}/script/max-posttarget.cmake)
