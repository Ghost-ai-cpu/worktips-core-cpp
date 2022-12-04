
cmake_minimum_required(VERSION 3.4.1)

SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

project(TEST)
enable_testing()

#Prep ourselves for compiling boost
find_package(Boost COMPONENTS 
    unit_test_framework REQUIRED
    system REQUIRED
    thread REQUIRED
)

set(WORKTIPS_SRC "contrib/worktips-core-custom")

include_directories(${Boost_INCLUDE_DIRS})
include_directories("src")
include_directories(${WORKTIPS_SRC})
include_directories("${WORKTIPS_SRC}/epee/include")
include_directories("${WORKTIPS_SRC}/common")
include_directories("${WORKTIPS_SRC}/vtlogger")
include_directories("${WORKTIPS_SRC}/crypto")
include_directories("${WORKTIPS_SRC}/cryptonote_basic")
include_directories("${WORKTIPS_SRC}/multisig")
include_directories("${WORKTIPS_SRC}/cryptonote_core")
include_directories("${WORKTIPS_SRC}/cryptonote_protocol")
include_directories("${WORKTIPS_SRC}/wallet")
include_directories("${WORKTIPS_SRC}/rpc")
include_directories("${WORKTIPS_SRC}/mnemonics")
include_directories("${WORKTIPS_SRC}/contrib/libsodium/include") # support sodium/… paths
include_directories("${WORKTIPS_SRC}/contrib/libsodium/include/sodium")
include_directories("test")

# keeping test files in a separate source directory 
file(GLOB TEST_SRCS RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} test/test_*.cpp)

set(
    SRC_FILES
    #
    src/worktips_address_utils.hpp
    src/worktips_address_utils.cpp
    src/worktips_paymentID_utils.hpp
    src/worktips_paymentID_utils.cpp
    src/worktips_key_image_utils.hpp
    src/worktips_key_image_utils.cpp
    src/worktips_fee_utils.hpp
    src/worktips_fee_utils.cpp
    src/worktips_transfer_utils.hpp
    src/worktips_transfer_utils.cpp
    src/worktips_send_routine.hpp
    src/worktips_send_routine.cpp
    src/worktips_fork_rules.hpp
    src/worktips_fork_rules.cpp
    src/worktips_wallet_utils.hpp
    src/worktips_wallet_utils.cpp
    src/serial_bridge_index.hpp
    src/serial_bridge_index.cpp
    src/serial_bridge_utils.hpp
    src/serial_bridge_utils.cpp
    src/tools__ret_vals.hpp
    src/tools__ret_vals.cpp
    #
    ${WORKTIPS_SRC}/cryptonote_basic/cryptonote_basic_impl.cpp
    ${WORKTIPS_SRC}/cryptonote_basic/account.cpp
    ${WORKTIPS_SRC}/cryptonote_basic/cryptonote_format_utils.cpp
    ${WORKTIPS_SRC}/crypto/crypto.cpp
    ${WORKTIPS_SRC}/crypto/hash.c
    ${WORKTIPS_SRC}/crypto/slow-hash.c
    ${WORKTIPS_SRC}/crypto/oaes_lib.c
    ${WORKTIPS_SRC}/crypto/crypto-ops.c
    ${WORKTIPS_SRC}/crypto/crypto-ops-data.c
    ${WORKTIPS_SRC}/crypto/keccak.c
    ${WORKTIPS_SRC}/crypto/chacha.c
    ${WORKTIPS_SRC}/crypto/random.c
    ${WORKTIPS_SRC}/crypto/aesb.c
    ${WORKTIPS_SRC}/crypto/tree-hash.c
    ${WORKTIPS_SRC}/crypto/hash-extra-blake.c
    ${WORKTIPS_SRC}/crypto/blake256.c
    ${WORKTIPS_SRC}/crypto/hash-extra-groestl.c
    ${WORKTIPS_SRC}/crypto/hash-extra-jh.c
    ${WORKTIPS_SRC}/crypto/hash-extra-skein.c
    ${WORKTIPS_SRC}/crypto/groestl.c
    ${WORKTIPS_SRC}/crypto/jh.c
    ${WORKTIPS_SRC}/crypto/skein.c
    ${WORKTIPS_SRC}/cryptonote_core/cryptonote_tx_utils.cpp
    ${WORKTIPS_SRC}/common/base58.cpp
    ${WORKTIPS_SRC}/common/threadpool.cpp
    ${WORKTIPS_SRC}/common/aligned.c
    ${WORKTIPS_SRC}/common/util.cpp
    ${WORKTIPS_SRC}/epee/src/hex.cpp
    ${WORKTIPS_SRC}/epee/src/string_tools.cpp
    ${WORKTIPS_SRC}/epee/src/memwipe.c
    ${WORKTIPS_SRC}/epee/src/mlocker.cpp
    ${WORKTIPS_SRC}/epee/src/wipeable_string.cpp
    ${WORKTIPS_SRC}/device/device.cpp
    ${WORKTIPS_SRC}/device/device_default.cpp
    ${WORKTIPS_SRC}/ringct/rctOps.cpp
    ${WORKTIPS_SRC}/ringct/rctTypes.cpp
    ${WORKTIPS_SRC}/ringct/rctCryptoOps.c
    ${WORKTIPS_SRC}/ringct/rctSigs.cpp
    ${WORKTIPS_SRC}/ringct/bulletproofs.cc
    ${WORKTIPS_SRC}/ringct/multiexp.cc
    ${WORKTIPS_SRC}/mnemonics/electrum-words.cpp
    ${WORKTIPS_SRC}/vtlogger/logger.cpp
    ${WORKTIPS_SRC}/contrib/libsodium/src/crypto_verify/verify.c
)
# needed for slow-hash
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -maes")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -maes")

#Run through each source
foreach(testSrc ${TEST_SRCS})
    # extract the filename without an extension (NAME_WE)
    get_filename_component(testName ${testSrc} NAME_WE)
    add_executable(
        ${testName}
        ${testSrc}
        ${SRC_FILES}
    )
    target_link_libraries(
        ${testName}
        #
        ${Boost_LIBRARIES} 
    )
    set_target_properties(
        ${testName} PROPERTIES 
        RUNTIME_OUTPUT_DIRECTORY
        ${CMAKE_CURRENT_SOURCE_DIR}/build/products
    )
    add_test(
        NAME ${testName} 
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/build/products 
        COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/build/products/${testName} --catch_system_error=yes
    )
endforeach(testSrc)
