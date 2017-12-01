cmake_minimum_required(VERSION 3.8)
project(grpc_greeter)

set(CMAKE_CXX_STANDARD 11)

set(SOURCE_FILES main.cpp)
add_executable(grpc_greeter ${SOURCE_FILES})


find_package(Protobuf REQUIRED)
if (NOT PROTOBUF_FOUND)
    message("protobuf not found")
    return()
endif()
file(GLOB ProtoFiles "./*.proto")
PROTOBUF_GENERATE_CPP(ProtoSources ProtoHeaders ${ProtoFiles})
set(ProtobufIncludePath ${CMAKE_CURRENT_BINARY_DIR}
        CACHE INTERNAL "Path to generated protobuf files.")

set(HEADER_FILES greeter.grpc.pb.h greeter.pb.h)
set(GRPC_SRC greeter.grpc.pb.cc greeter.pb.cc)
set(SERVER_SRC greeter_server.cpp   ${GRPC_SRC} ${HEADER_FILES})
set(CLIENT_SRC greeter_client.cpp ${GRPC_SRC} ${HEADER_FILES})

add_executable(greeter_svr ${SERVER_SRC})
add_executable(greeter_client ${CLIENT_SRC})

target_link_libraries(greeter_svr grpc++ grpc grpc++_reflection protobuf pthread dl)
target_link_libraries(greeter_client grpc++ grpc grpc++_reflection protobuf pthread dl)