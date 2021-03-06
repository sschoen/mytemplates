cmake_minimum_required(VERSION 3.12)

project(websocket C CXX)

set(Boost_USE_STATIC_LIBS ON)

find_package(Boost REQUIRED COMPONENTS filesystem)
find_package(JSON REQUIRED)
find_package(CLI11 REQUIRED)
find_package(Spdlog REQUIRED)

set(_SOURCES
  src/main.cpp
  src/util.cpp
  src/http_server.cpp
  src/http_session.cpp
  src/websocket_session.cpp
)

add_executable(websocket-server ${_SOURCES})
# SPDLOG_TRACE を含めるならこれを定義する
# target_compile_definitions(websocket-server PRIVATE SPDLOG_ACTIVE_LEVEL=SPDLOG_LEVEL_TRACE)
set_target_properties(websocket-server PROPERTIES CXX_STANDARD 17 C_STANDARD 99)

if (ENABLE_TSAN)
  # tsan
  set_target_properties(${name} PROPERTIES
    COMPILE_OPTIONS "-g;-fsanitize=thread"
    LINK_OPTIONS    "-g;-fsanitize=thread"
    INTERFACE_LINK_OPTIONS    "-g;-fsanitize=thread")
endif()

if (ENABLE_ASAN)
  # asan
  set_target_properties(${name} PROPERTIES
    COMPILE_FLAGS "-g;-fsanitize=address;-fno-omit-frame-pointer"
    LINK_OPTIONS  "-g;-fsanitize=address;-fno-omit-frame-pointer"
    INTERFACE_LINK_OPTIONS  "-g;-fsanitize=address;-fno-omit-frame-pointer")
endif()

target_link_libraries(websocket-server
  Boost::filesystem
  JSON::JSON
  CLI11::CLI11
  Spdlog::Spdlog)
