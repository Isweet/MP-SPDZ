cmake_minimum_required (VERSION 3.10)
project (spdz)
set(NAME "spdz")

set(CMAKE_BUILD_TYPE Debug)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")
set(CMAKE_VERBOSE_MAKEFILE ON)

if (APPLE)
  set(OPENSSL_ROOT_DIR "/usr/local/opt/openssl")
endif()

find_package(openssl REQUIRED)
find_package(sodium REQUIRED)
find_package(mpir REQUIRED)
find_package(Boost REQUIRED system)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_C_FLAGS "-march=native -O3 -Wno-error=deprecated-declarations -fPIC")
set(CMAKE_CXX_FLAGS "-march=native -O3 -Wno-error=deprecated-declarations -fPIC")

file(GLOB sources
  "Math/*.cpp"
  "Tools/*.cpp"
  "Networking/*.cpp"
  "Processor/*.cpp"
  "OT/*.cpp")

set(sources
  ${sources}
  GC/square64.cpp
  GC/Instruction.cpp
  GC/SemiSecret.cpp
  GC/SemiPrep.cpp)

add_library(spdz SHARED ${sources})

target_link_libraries(
  spdz
  ${sodium_LIBRARY_DEBUG}
  ${MPIR_LIBRARIES}
  ${OPENSSL_LIBRARIES}
  ${Boost_LIBRARIES}
  ${CMAKE_SOURCE_DIR}/SimpleOT/libsimpleot.a
  )

target_include_directories(
  spdz PUBLIC
  ${sodium_INCLUDE_DIR}
  ${MPIR_INCLUDE_DIR}
  ${OPENSSL_INCLUDE_DIR}
  ${Boost_INCLUDE_DIRS}
  .)
