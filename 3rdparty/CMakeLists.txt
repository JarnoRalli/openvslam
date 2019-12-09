project(Content)
include(FetchContent)

message(STATUS "-------------")
message(STATUS "FETCH CONTENT")
message(STATUS "-------------")

#-------------
# Google test
#-------------
FetchContent_Declare(
  googletest
  GIT_REPOSITORY https://github.com/google/googletest.git
  GIT_TAG release-1.10.0
  GIT_SHALLOW TRUE
)

FetchContent_GetProperties(googletest)
if(NOT googletest_POPULATED AND USE_FETCH_GOOGLETEST)
  message(STATUS "\tFetch: Google test")
  FetchContent_Populate(googletest)
  #By default Google Test uses /Mt run-time linking which might cause problems, so we force it to use shared run-time
  set(gtest_force_shared_crt TRUE CACHE BOOL "Use shared (DLL) run-time lib even when Google Test is built as static lib." FORCE)
  message(STATUS "\t\tsource dir: ${googletest_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${googletest_BINARY_DIR}")
  add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})
  message(STATUS "\tFetch: done")
endif()

#-------------
# Google log
#-------------
FetchContent_Declare(
  glog
  GIT_REPOSITORY https://github.com/google/glog.git
  GIT_TAG v0.4.0
  GIT_SHALLOW TRUE
)

FetchContent_GetProperties(glog)
if(NOT glog_POPULATED AND USE_FETCH_GLOG)
  message(STATUS "\tFetch: Google log (glog)")
  FetchContent_Populate(glog)
  set(WITH_GFLAGS FALSE CACHE BOOL "Use gflags" FORCE)
  message(STATUS "\t\tsource dir: ${glog_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${glog_BINARY_DIR}")
  add_subdirectory(${glog_SOURCE_DIR} ${glog_BINARY_DIR})
  message(STATUS "\tFetch: done")
endif()

#----------------
# JSON_BuildTests
#-----------------
option(JSON_BuildTests "" OFF)
option(JSON_MultipleHeaders "" ON)
FetchContent_Declare(
  nlohmann_json
  GIT_REPOSITORY https://github.com/nlohmann/json.git
  GIT_TAG v3.7.3
  GIT_SHALLOW TRUE
)

FetchContent_GetProperties(nlohmann_json)
if(NOT nlohmann_json_POPULATED AND USE_FETCH_JSON)
  message(STATUS "\tFetch: nlohmann_json")
  FetchContent_Populate(nlohmann_json)
  message(STATUS "\t\ttsource dir: ${nlohmann_json_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${nlohmann_json_BINARY_DIR}")
  add_subdirectory(${nlohmann_json_SOURCE_DIR} ${nlohmann_json_BINARY_DIR})
  message(STATUS "\tFetch: done")
endif()

#---------------
# Fast logging
#---------------
FetchContent_Declare(
  spdlog
  GIT_REPOSITORY https://github.com/gabime/spdlog.git
  GIT_TAG v1.4.2
  GIT_SHALLOW TRUE
)

FetchContent_GetProperties(spdlog)
if(NOT spdlog_POPULATED AND USE_FETCH_SPDLOG)
  message(STATUS "\tFetch: spdlog")
  FetchContent_Populate(spdlog)
  message(STATUS "\t\tsource dir: ${spdlog_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${spdlog_BINARY_DIR}")
  add_subdirectory(${spdlog_SOURCE_DIR} ${spdlog_BINARY_DIR})
  message("\tFetch: done")
endif()

#Dirty hack to force installation of spdlog
install(TARGETS spdlog
    EXPORT ${OPENVSLAM_TARGETS_EXPORT_NAME}
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
)

#----------
# Yaml-cpp
#----------
option(YAML_CPP_BUILD_TESTS "" OFF)
FetchContent_Declare(
  yaml-cpp
  GIT_REPOSITORY https://github.com/jbeder/yaml-cpp.git
  GIT_TAG yaml-cpp-0.6.3
  GIT_SHALLOW TRUE
)

FetchContent_GetProperties(yaml-cpp)
if(NOT yaml_cpp_POPULATED AND USE_FETCH_YAML)
  message(STATUS "\tFetch: yaml-cpp")
  FetchContent_Populate(yaml-cpp)
  message(STATUS "\t\tsource dir: ${yaml-cpp_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${yaml-cpp_BINARY_DIR}")
  add_subdirectory(${yaml-cpp_SOURCE_DIR} ${yaml-cpp_BINARY_DIR})
  message("\tFetch: done")
endif()

#-----------
# Socket.IO
#-----------
option(BUILD_UNIT_TESTS "" OFF)
FetchContent_Declare(
  socket.io-client
  GIT_REPOSITORY https://github.com/shinsumicco/socket.io-client-cpp.git
)

FetchContent_GetProperties(socket.io-client)
if(NOT socket.io-client_POPULATED AND USE_FETCH_SOCKET_IO)
  message(STATUS "\tFetch: socket.io-client")
  FetchContent_Populate(socket.io-client)
  message(STATUS "\t\tsource dir: ${socket.io-client_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${socket.io-client_BINARY_DIR}")
  add_subdirectory(${socket.io-client_SOURCE_DIR} ${socket.io-client_BINARY_DIR})
  message("\tFetch: done")
endif()

#--------------
# Protobuf cpp
#--------------
option(protobuf_BUILD_TESTS "" OFF)
option(protobuf_MSVC_STATIC_RUNTIME "" OFF)
option(protobuf_BUILD_SHARED_LIBS "" OFF)
FetchContent_Declare(
  protobuf
  URL      https://github.com/protocolbuffers/protobuf/releases/download/v3.11.2/protobuf-cpp-3.11.2.zip
)

FetchContent_GetProperties(protobuf)
if(NOT protobuf_POPULATED)
  message(STATUS "\tFetch: protobuf")
  FetchContent_Populate(protobuf)
  message(STATUS "\t\tsource dir: ${protobuf_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${protobuf_BINARY_DIR}")
  message("\tFetch: done")
endif()

#---------------------
# Protobuf executable
#--------------------
if(${CMAKE_HOST_SYSTEM_NAME} STREQUAL "Windows")
    set(protobuf-exe-url "https://github.com/protocolbuffers/protobuf/releases/download/v3.11.2/protoc-3.11.2-win64.zip")
elseif(${CMAKE_HOST_SYSTEM_NAME} STREQUAL "Linux")
    set(protobuf-exe-url "https://github.com/protocolbuffers/protobuf/releases/download/v3.11.2/protoc-3.11.2-linux-x86_64.zip")
elseif(${CMAKE_HOST_SYSTEM_NAME} STREQUAL "Darwin")
    set(protobuf-exe-url "https://github.com/protocolbuffers/protobuf/releases/download/v3.11.2/protoc-3.11.2-osx-x86_64.zip")
else()
    message(FATAL_ERROR "Can only fetch protobuf for Windows/Linux/OSX")
endif()

FetchContent_Declare(
  protobuf-exe
  URL      ${protobuf-exe-url}
)

FetchContent_GetProperties(protobuf-exe)
if(NOT protobuf-exe_POPULATED)
  message(STATUS "\tFetch: protobuf executable")
  FetchContent_Populate(protobuf-exe)
  message(STATUS "\t\tsource dir: ${protobuf-exe_SOURCE_DIR}")
  message(STATUS "\t\tbinary dir: ${protobuf-exe_BINARY_DIR}")
  message("\tFetch: done")
endif()

#-------------------------------
# Program options parser (popl)
#-------------------------------
add_subdirectory(popl) # Source https://github.com/badaix/popl.git

#--------
# Dirent
#--------
if(WIN32)
    add_subdirectory(dirent) #Source https://github.com/tronkko/dirent.git
endif(WIN32)
