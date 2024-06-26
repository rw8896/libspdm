cmake_minimum_required(VERSION 3.5)

include_directories(${LIBSPDM_DIR}/unit_test/test_spdm_requester
                    ${LIBSPDM_DIR}/include
                    ${LIBSPDM_DIR}/unit_test/include
                    ${LIBSPDM_DIR}/os_stub/spdm_device_secret_lib_sample
                    ${LIBSPDM_DIR}/unit_test/cmockalib/cmocka/include
                    ${LIBSPDM_DIR}/unit_test/cmockalib/cmocka/include/cmockery
                    ${LIBSPDM_DIR}/unit_test/spdm_unit_test_common
                    ${LIBSPDM_DIR}/os_stub
)

if(CMAKE_SYSTEM_NAME MATCHES "Windows")
    if((TOOLCHAIN STREQUAL "VS2015") OR (TOOLCHAIN STREQUAL "VS2019") OR (TOOLCHAIN STREQUAL "VS2022"))
        add_compile_options(/wd4819)
    endif()
endif()

set(src_test_secured_message
    test_spdm_secured_message.c
    encode_decode.c
    ${LIBSPDM_DIR}/unit_test/spdm_unit_test_common/common.c
    ${LIBSPDM_DIR}/unit_test/spdm_unit_test_common/algo.c
    ${LIBSPDM_DIR}/unit_test/spdm_unit_test_common/support.c
)

set(test_spdm_secured_message_LIBRARY
    memlib
    debuglib
    spdm_responder_lib
    spdm_requester_lib
    spdm_common_lib
    ${CRYPTO_LIB_PATHS}
    rnglib
    cryptlib_${CRYPTO}
    malloclib
    spdm_crypt_lib
    spdm_crypt_ext_lib
    spdm_secured_message_lib
    spdm_device_secret_lib_sample
    spdm_transport_test_lib
    cmockalib
    platform_lib_null
)

if(TOOLCHAIN STREQUAL "ARM_DS2022")
    set(test_spdm_common_LIBRARY ${test_spdm_common_LIBRARY} armbuild_lib)
endif()

if(NOT ((TOOLCHAIN STREQUAL "KLEE") OR (TOOLCHAIN STREQUAL "CBMC")))
    add_executable(test_spdm_secured_message ${src_test_secured_message})
    target_link_libraries(test_spdm_secured_message ${test_spdm_secured_message_LIBRARY})
endif()
