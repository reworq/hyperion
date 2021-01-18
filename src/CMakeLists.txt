set(TARGET_NAME "hyperion")

file(
    GLOB TARGET_SRC
    LIST_DIRECTORIES FALSE
    CONFIGURE_DEPENDS
    "${HYPERION_SOURCE_DIR}/Src/Crypter/*.c"
    "${HYPERION_SOURCE_DIR}/Src/Payloads/Aes/c/*.c"
)

set(TARGET_INCLUDE_DIRS
    "${HYPERION_SOURCE_DIR}/Src/Payloads/Aes/c"
)

add_executable(${TARGET_NAME})
target_sources(${TARGET_NAME} PRIVATE ${TARGET_SRC})
target_include_directories(${TARGET_NAME} PRIVATE ${TARGET_INCLUDE_DIRS})

set_target_properties(
    ${TARGET_NAME}
    PROPERTIES
        OUTPUT_NAME "Runtime"
        ARCHIVE_OUTPUT_DIRECTORY "${HYPERION_BINARY_DIR}"
        LIBRARY_OUTPUT_DIRECTORY "${HYPERION_BINARY_DIR}"
        RUNTIME_OUTPUT_DIRECTORY "${HYPERION_BINARY_DIR}"
)

add_custom_command(
    TARGET ${TARGET_NAME}
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory "${HYPERION_SOURCE_DIR}/Src/Container" "${HYPERION_BINARY_DIR}/Container"
    COMMAND ${CMAKE_COMMAND} -E copy_directory "${HYPERION_SOURCE_DIR}/Src/Payloads/Aes/32" "${HYPERION_BINARY_DIR}/Payloads/Aes/32"
    COMMAND ${CMAKE_COMMAND} -E copy_directory "${HYPERION_SOURCE_DIR}/Src/Payloads/Aes/64" "${HYPERION_BINARY_DIR}/Payloads/Aes/64"
    BYPRODUCTS ${TARGET_SRC}
    WORKING_DIRECTORY "${HYPERION_BINARY_DIR}"
    COMMENT "Copying extra files..."
    VERBATIM
)

install(
    DIRECTORY "${HYPERION_BINARY_DIR}/"
    DESTINATION "."
)
