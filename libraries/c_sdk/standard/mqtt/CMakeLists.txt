afr_module()

afr_set_lib_metadata(ID "mqtt")
afr_set_lib_metadata(DESCRIPTION "This library implements the MQTT protocol that enables \
communication with AWS IoT. MQTT is an ISO standard publish-subscribe-based messaging protocol.")
afr_set_lib_metadata(DISPLAY_NAME "MQTT")
afr_set_lib_metadata(CATEGORY "Connectivity")
afr_set_lib_metadata(VERSION "1.0.0")
afr_set_lib_metadata(IS_VISIBLE "true")

set(src_dir "${CMAKE_CURRENT_LIST_DIR}/src")
set(inc_dir "${CMAKE_CURRENT_LIST_DIR}/include")
set(test_dir "${CMAKE_CURRENT_LIST_DIR}/test")


# Enable test access and mocks if building tests.
if(${AFR_IS_TESTING})
    list(APPEND extra_mqtt_test_includes "${test_dir}/access")
    list(APPEND extra_mqtt_test_includes "${test_dir}/mock")
endif()

afr_module_sources(
    ${AFR_CURRENT_MODULE}
    PRIVATE
        "${src_dir}/iot_mqtt_api.c"
        "${src_dir}/iot_mqtt_network.c"
        "${src_dir}/iot_mqtt_operation.c"
        "${src_dir}/iot_mqtt_publish_duplicates.c"
        "${src_dir}/iot_mqtt_static_memory.c"
        "${src_dir}/iot_mqtt_subscription.c"
        "${src_dir}/iot_mqtt_validate.c"
        "${src_dir}/iot_mqtt_context_connection.c"
        "${src_dir}/iot_mqtt_serializer_deserializer_wrapper.c"
        "${src_dir}/iot_mqtt_managed_function_wrapper.c"
        "${src_dir}/iot_mqtt_subscription_container.c"
        "${src_dir}/iot_mqtt_mutex_wrapper.c"
        ${extra_mqtt_sources}
)

if(TARGET AFR::secure_sockets::mcu_port)
afr_module_sources(
    ${AFR_CURRENT_MODULE}
    PRIVATE
        "${src_dir}/iot_mqtt_agent.c"
)
endif()

afr_module_include_dirs(
    ${AFR_CURRENT_MODULE}
    PUBLIC
        "${inc_dir}"
        "${AFR_MODULES_DIR}/c_sdk/standard/logging_stack"
        "$<${AFR_IS_TESTING}:${src_dir}>"
    PRIVATE
        ${extra_mqtt_includes}
        ${extra_mqtt_test_includes}
)

afr_module_dependencies(
    ${AFR_CURRENT_MODULE}
    PUBLIC
        AFR::common
        ${extra_mqtt_dependencies}
        AFR::core_mqtt
)

# MQTT test
afr_test_module()

afr_module_sources(
    ${AFR_CURRENT_MODULE}
    INTERFACE
        "${test_dir}/mock/iot_tests_mqtt_mock.c"
        "${test_dir}/unit/iot_tests_mqtt_api.c"
        "${test_dir}/unit/iot_tests_mqtt_receive.c"
        "${test_dir}/unit/iot_tests_mqtt_subscription.c"
        "${test_dir}/unit/iot_tests_mqtt_validate.c"
        "${test_dir}/unit/iot_tests_mqtt_metrics.c"
        "${test_dir}/system/iot_tests_mqtt_system.c"
        ${extra_test_mqtt_sources}
)

if(TARGET AFR::secure_sockets::mcu_port)
afr_module_sources(
    ${AFR_CURRENT_MODULE}
    INTERFACE
        "${test_dir}/iot_test_mqtt_agent.c"
)
endif()

afr_module_include_dirs(
    ${AFR_CURRENT_MODULE}
    INTERFACE
        "${test_dir}/access"
        "${test_dir}/mock"
)
afr_module_dependencies(
    ${AFR_CURRENT_MODULE}
    INTERFACE AFR::mqtt
)
