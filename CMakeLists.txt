cmake_minimum_required(VERSION 3.12)

project(cosemlib VERSION 1.0
                  DESCRIPTION "DLMS/COSEM stack library"
                  LANGUAGES C)
                  
option(COSEM_BUILD_EXAMPLES "Build examples" OFF)

add_library(cosemlib STATIC )

target_include_directories(cosemlib PRIVATE 
    src
    share/crypto
    share/ip
    share/serial
    share/util
    mbedtls/include
)

if(WIN32)
  target_compile_definitions(cosemlib PRIVATE
    USE_WINDOWS_OS
  )
elseif(UNIX)
  target_compile_definitions(cosemlib PRIVATE
    USE_UNIX_OS
  )
endif()

if(MSVC)
  target_compile_definitions(cosemlib PRIVATE
    _CRT_SECURE_NO_WARNINGS
  )
elseif(CMAKE_COMPILER_IS_GNUCXX)
  add_compile_options(
        -Wno-unused-function
        -Wno-undef
  )
endif()

set(COSEMLIB_SOURCES
  hdlc/hdlc.c
  share/crypto/gcm.c
  share/crypto/sha1.c
  share/crypto/cipher.c
  share/crypto/cmac.c
  share/crypto/cipher_wrap.c
  share/crypto/aes.c
  share/crypto/md5.c
  share/crypto/sha256.c
  share/ip/tcp_client.c
  share/ip/tcp_server.c
  share/util/bitfield.c
  share/util/os_util.c
  share/util/clock.c
  share/serial/serial.c
  src/csm_axdr_codec.c
  src/csm_security.c
  src/csm_array.c
  src/csm_association.c
  src/csm_ber.c
  src/csm_channel.c
  src/csm_services.c
)

set (COSEMLIB_HEADERS
  hdlc/hdlc_config.h
  hdlc/hdlc.h
  share/crypto/md5.h
  share/crypto/gcm.h
  share/crypto/cmac.h
  share/crypto/aes.h
  share/crypto/sha256.h
  share/crypto/cipher_internal.h
  share/crypto/sha1.h
  share/crypto/cipher.h
  share/crypto/config.h
  share/ip/tcp_client.h
  share/ip/tcp_server.h
  share/util/os_util.h
  share/util/bitfield.h
  share/util/clock.h
  share/util/transports.h
  share/serial/serial.h
  src/csm_association.h
  src/csm_security.h
  src/csm_channel.h
  src/csm_ber.h
  src/csm_config.h
  src/csm_definitions.h
  src/csm_axdr_codec.h
  src/csm_services.h
  src/csm_array.h
)

target_sources( cosemlib PRIVATE
     ${COSEMLIB_SOURCES}
     ${COSEMLIB_HEADERS}
)

if(COSEM_BUILD_EXAMPLES)
    add_subdirectory(cosemserver)
    add_subdirectory(cosemclient)
endif()

include(CMakePrintHelpers)
include(FeatureSummary)

# Configuration information
cmake_print_variables(CMAKE_SYSTEM_NAME)
cmake_print_variables(CMAKE_CXX_COMPILER)
get_property(_isMultiConfig GLOBAL PROPERTY GENERATOR_IS_MULTI_CONFIG)
if(_isMultiConfig)
  cmake_print_variables(CMAKE_CONFIGURATION_TYPES)
else()
  cmake_print_variables(CMAKE_BUILD_TYPE)
endif()

cmake_print_variables(COSEM_BUILD_EXAMPLES)
message(STATUS "Feature Summary:" )
feature_summary(WHAT ENABLED_FEATURES DISABLED_FEATURES PACKAGES_FOUND)
