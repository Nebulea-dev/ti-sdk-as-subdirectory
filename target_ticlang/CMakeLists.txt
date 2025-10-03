find_package(DeviceCC23X0R5 REQUIRED)

add_executable(target)

set(SDK_DIR "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01")

target_include_directories(target PUBLIC
    .
    Profiles
    syscfg
    "${SDK_DIR}/source/ti/bleapp/profiles/simple_gatt"
    "${SDK_DIR}/source/ti/bleapp/menu_module"
    "${SDK_DIR}/source/ti/ble5stack_flash/icall/src/inc"
    "${SDK_DIR}/source/ti/bleapp/ble_app_util/inc"
    "${SDK_DIR}/source/ti/bleapp/services/dev_info"
    "${SDK_DIR}/source/ti/common/nv"
    "${SDK_DIR}/source"
)


target_link_libraries(target PRIVATE
    ${SDK_DIR}/source/ti/ble5stack_flash/lib_projects/CC2340R5/OneLib/lib/ticlang/m0p/OneLib.a
    ${SDK_DIR}/source/ti/ble5stack_flash/lib_projects/CC2340R5/StackWrapper/lib/ticlang/m0p/StackWrapper.a
    display_cc23x0r5
    FreeRTOS::freertos
    drivers_cc23x0r5
    Bluetooth::ble
    RCL::cc23x0r5
    ${SDK_DIR}/source/third_party/ecc/lib/ticlang/m0p/ecc.a
)

target_sources(target PRIVATE
    main_freertos.c
    app_broadcaster.c
    app_central.c
    app_connection.c
    app_data.c
    app_l2cap_coc.c
    app_main.c
    app_menu.c
    app_observer.c
    app_pairing.c
    app_peripheral.c
    Profiles/app_dev_info.c
    Profiles/app_simple_gatt.c

    syscfg/ti_ble_config.c
    syscfg/ti_devices_config.c
    syscfg/ti_drivers_config.c
    syscfg/ti_freertos_config.c
    syscfg/ti_freertos_portable_config.c
    syscfg/ti_radio_config.c

    ${SDK_DIR}/source/ti/bleapp/ble_app_util/src/bleapputil_init.c
    ${SDK_DIR}/source/ti/bleapp/ble_app_util/src/bleapputil_process.c
    ${SDK_DIR}/source/ti/bleapp/ble_app_util/src/bleapputil_stack_callbacks.c
    ${SDK_DIR}/source/ti/bleapp/ble_app_util/src/bleapputil_task.c
    ${SDK_DIR}/source/ti/common/nv/crc.c
    ${SDK_DIR}/source/ti/common/nv/nvocmp.c
    ${SDK_DIR}/source/ti/ble5stack_flash/icall/src/icall_cc23x0.c
    ${SDK_DIR}/source/ti/ble5stack_flash/icall/src/icall_user_config.c
    ${SDK_DIR}/source/ti/ble5stack_flash/icall/src/icall_POSIX.c
    ${SDK_DIR}/source/ti/ble5stack_flash/icall/app/ble_stack_api.c
    ${SDK_DIR}/source/ti/ble5stack_flash/icall/app/ble_user_config.c
    ${SDK_DIR}/source/ti/ble5stack_flash/icall/app/icall_api_lite.c
    ${SDK_DIR}/source/ti/bleapp/menu_module/menu_module.c
    ${SDK_DIR}/source/ti/bleapp/profiles/simple_gatt/simple_gatt_profile.c
    ${SDK_DIR}/source/ti/bleapp/services/dev_info/dev_info_service.c
    ${SDK_DIR}/source/ti/ble5stack_flash/icall/stack/ble_user_config_stack.c
    ${SDK_DIR}/source/ti/ble5stack_flash/rom/agama_r1/rom_init.c

    common/Startup/osal_icall_ble.c
)

# Project specific defined, not created by sysconfig. 
# Can be seen in the project configuration of Basic BLE in CCS
add_compile_definitions(ICALL_NO_APP_EVENTS CC23X0 NVOCMP_NWSAMEITEM=1 FLASH_ONLY_BUILD NVOCMP_NVPAGES=6 FREERTOS NVOCMP_POSIX_MUTEX)

# Add all defines from ti_utils_build_compiler.opt
file(READ "${CMAKE_CURRENT_LIST_DIR}/syscfg/ti_utils_build_compiler.opt" FILE_CONTENTS)
string(REPLACE "-D" "" FILE_CONTENTS ${FILE_CONTENTS})
string(REPLACE "\n" ";" DEFINE_SYSCFG ${FILE_CONTENTS})

foreach(DEFINITION ${DEFINE_SYSCFG})
    add_compile_definitions(${DEFINITION})
endforeach()


# Add all defines from build_components.opt
file(READ "${SDK_DIR}/source/ti/ble5stack_flash/config/build_components.opt" FILE_CONTENTS)
string(REPLACE "-D" "" FILE_CONTENTS ${FILE_CONTENTS})
string(REPLACE "\n" ";" DEFINE_SYSCFG ${FILE_CONTENTS})

foreach(DEFINITION ${DEFINE_SYSCFG})
    add_compile_definitions(${DEFINITION})
endforeach()


# Add all defines from factory_config.opt
file(READ "${SDK_DIR}/source/ti/ble5stack_flash/config/factory_config.opt" FILE_CONTENTS)
string(REPLACE "-D" "" FILE_CONTENTS ${FILE_CONTENTS})
string(REPLACE "\n" ";" DEFINE_SYSCFG ${FILE_CONTENTS})

foreach(DEFINITION ${DEFINE_SYSCFG})
    add_compile_definitions(${DEFINITION})
endforeach()


# Add all defines from ti_utils_build_linker.cmd.genlibs
file(READ "${CMAKE_CURRENT_LIST_DIR}/syscfg/ti_utils_build_linker.cmd.genlibs" FILE_CONTENTS)
string(REPLACE "\n" ";" LIBS_SYSCFG ${FILE_CONTENTS})

foreach(LIB ${LIBS_SYSCFG})
    if(${LIB} MATCHES "-l")
        string(REPLACE "-l" "" LIB_PATH ${LIB})
        string(REPLACE "\"" "" LIB_PATH_QUOTELESS ${LIB_PATH})
        #target_link_libraries(target PRIVATE "${SDK_DIR}/source/${LIB_PATH_QUOTELESS}")
    endif()
endforeach()

target_link_options(target PRIVATE 
    "${CMAKE_CURRENT_LIST_DIR}/lpf3_app_freertos.cmd"
)

set(CRC_TOOL "${SDK_DIR}/tools/common/crc_tool/crc_tool")

add_custom_command(
    TARGET target
    POST_BUILD
    COMMAND ${CRC_TOOL} patch-image --elf "$<TARGET_FILE:target>" --output "$<TARGET_FILE:target>" --symbol-prefix ti_utils_build_GenMap_sym_CRC
)
