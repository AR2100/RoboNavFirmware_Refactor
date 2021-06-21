cmake_minimum_required(VERSION 3.9)

project(igvc-firwmware-mbed C CXX ASM)

set(CMAKE_SYSTEM_NAME Generic)
#SET(CMAKE_SYSTEM_PROCESSOR arm)
set(CMAKE_CROSSCOMPILING TRUE)

# force compiler settings
set(CMAKE_C_COMPILER_WORKS TRUE)
set(CMAKE_CXX_COMPILER_WORKS TRUE)

# force cmake compilers
set(CMAKE_ASM_COMPILER    "arm-none-eabi-gcc")
set(CMAKE_C_COMPILER      "arm-none-eabi-gcc")
set(CMAKE_CXX_COMPILER    "arm-none-eabi-g++")
set(ELF2BIN               "arm-none-eabi-objcopy")

# if the environment does not specify build type, set to Debug
IF(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE "Debug"
    CACHE STRING "Choose the type of build, options are: Debug Release RelWithDebInfo MinSizeRel."
    FORCE)
ENDIF()

# =================
# = NanoPB config =
# =================
message(STATUS "Creating NanoPB target...")

set(NANOPB_SRC_ROOT_FOLDER ${CMAKE_CURRENT_SOURCE_DIR}/external/nanopb)
set(CMAKE_MODULE_PATH ${NANOPB_SRC_ROOT_FOLDER}/extra)
find_package(Nanopb REQUIRED)

include_directories(${NANOPB_INCLUDE_DIRS})
set(PROTOS protos/igvc.proto)
nanopb_generate_cpp(PROTO_GENERATED_SRCS PROTO_HDRS ${PROTOS})
include_directories(${CMAKE_CURRENT_BINARY_DIR})
# Find the cpp files in the nanopb repo though and mark those as not generated, otherwise make clean removes them
file(GLOB PROTO_SRCS "${NANOPB_SRC_ROOT_FOLDER}/*.c" "${NANOPB_SRC_ROOT_FOLDER}/*.h")

set_source_files_properties(${PROTO_GENERATED_SRCS} ${PROTO_HDRS} PROPERTIES GENERATED TRUE)
set_source_files_properties(${PROTO_SRCS} PROPERTIES GENERATED FALSE)

message(STATUS "Creating NanoPB target...Done!")

