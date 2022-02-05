cmake_minimum_required (VERSION 3.22.2)
project (spdz VERSION 0.0.1)
set(NAME "spdz")

set(CMAKE_BUILD_TYPE Debug)
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")
set(CMAKE_VERBOSE_MAKEFILE ON)

if (APPLE)
  set(OPENSSL_ROOT_DIR "/usr/local/opt/openssl")
endif()

find_package(OpenSSL REQUIRED)
find_package(Sodium REQUIRED)
find_package(MPIR REQUIRED)
find_package(Boost REQUIRED COMPONENTS system)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)
set(CMAKE_C_FLAGS "-march=native -O3")
set(CMAKE_CXX_FLAGS "-march=native -O3")

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
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>)

install(TARGETS spdz
        DESTINATION lib
        EXPORT spdzTargets)

file(GLOB math-headers
  "Math/*.h"
  "Math/*.hpp")
install(FILES ${math-headers} DESTINATION include/Math)

file(GLOB tools-headers
  "Tools/*.h"
  "Tools/*.hpp")
install(FILES ${tools-headers} DESTINATION include/Tools)

file(GLOB networking-headers
  "Networking/*.h"
  "Networking/*.hpp")
install(FILES ${networking-headers} DESTINATION include/Networking)

file(GLOB processor-headers
  "Processor/*.h"
  "Processor/*.hpp")
install(FILES ${processor-headers} DESTINATION include/Processor)

file(GLOB ot-headers
  "OT/*.h"
  "OT/*.hpp")
install(FILES ${ot-headers} DESTINATION include/OT)

file(GLOB gc-headers
  "GC/*.h"
  "GC/*.hpp")
install(FILES ${gc-headers} DESTINATION include/GC)

file(GLOB protocols-headers
 "Protocols/*.h"
 "Protocols/*.hpp")
install(FILES ${protocols-headers} DESTINATION include/Protocols)

file(GLOB machine-headers
 "Machines/*.h"
 "Machines/*.hpp")
install(FILES ${machine-headers} DESTINATION include/Machines)

file(GLOB bmr-headers
 "BMR/*.h"
 "BMR/*.hpp")
install(FILES ${bmr-headers} DESTINATION include/BMR)

file(GLOB fhe-headers
 "FHE/*.h"
 "FHE/*.hpp")
install(FILES ${fhe-headers} DESTINATION include/FHE)

file(GLOB fhe-offline-headers
 "FHEOffline/*.h"
 "FHEOffline/*.hpp")
install(FILES ${fhe-offline-headers} DESTINATION include/FHEOffline)

install(EXPORT spdzTargets
        FILE spdzTargets.cmake
        DESTINATION lib/cmake/spdz)

include(CMakePackageConfigHelpers)

configure_package_config_file(${CMAKE_CURRENT_SOURCE_DIR}/Config.cmake.in
  "${CMAKE_CURRENT_BINARY_DIR}/spdzConfig.cmake"
  INSTALL_DESTINATION "lib/cmake/spdz"
  NO_SET_AND_CHECK_MACRO
  NO_CHECK_REQUIRED_COMPONENTS_MACRO)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/spdzConfig.cmake
  DESTINATION lib/cmake/spdz)

export(EXPORT spdzTargets
  FILE "${CMAKE_CURRENT_BINARY_DIR}/spdzTargets.cmake")
