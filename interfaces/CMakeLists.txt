project(robotinterface)

set(ROBOT_PROTO_SOURCES robot.proto)
set(ROBOT_PROTO_C_FILE robot.pb.c)
set(ROBOT_PROTO_H_FILE robot.pb.h)

if(NOT DEFINED NANOPB_ROOT)
    if(DEFINED ENV{NANOPB_ROOT})
        set(NANOPB_ROOT "$ENV{NANOPB_ROOT}")
    else()
        message(WARNING "NANOPB_ROOT is undefined.\nIf nanopb fails to "
            "be found, try setting -DNANOPB_ROOT=/path/to/nanopb on the CMake "
            "command line.\n")
    endif()
endif()

set(PROTOC "${NANOPB_ROOT}/generator-bin/protoc")

# The --nanopb_out parameter serves double duty in protoc:
#   --nanopb_out=[generator-parameter:]output-directory
# In this case I'm using the optional generator-parameter part of it to pass
# '-x rpc-options.proto' to the nanopb generator, which suppresses a
#   #include "rpc-options.pb.h"
# from being generated in output files which import rpc-options.proto.
# rpc-options.proto is never itself compiled, so no such header file will ever
# exist. Is this flag documented somewhere in nanopb? Of course not.
add_custom_command(OUTPUT ${ROBOT_PROTO_C_FILE} ${ROBOT_PROTO_H_FILE}
    COMMAND ${PROTOC} -I. -I${NANOPB_ROOT}/generator/proto
        --nanopb_out=-x\ rpc-options.proto:${CMAKE_CURRENT_BINARY_DIR} ${ROBOT_PROTO_SOURCES}
    MAIN_DEPENDENCY ${PROJECT_SOURCE_DIR}/${ROBOT_PROTO_SOURCES}
    WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
    COMMENT "Generating test Widget protocol buffers ..."
    VERBATIM)

add_library(robotpb OBJECT ${ROBOT_PROTO_C_FILE})
target_include_directories(robotpb
    PRIVATE ${NANOPB_ROOT})
#add_custom_target(robot_pb_c DEPENDS ${ROBOT_PROTO_C_FILE})
