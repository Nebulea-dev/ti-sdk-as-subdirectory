find_package(DeviceCC23X0R5 REQUIRED)
#find_package(FreeRTOS REQUIRED)

add_executable(target)

target_include_directories(target PUBLIC
    .
    Profiles
    syscfg
    common/BLEAppUtil/inc
    common/Drivers/NV
    common/iCall
    common/iCallBLE
    common/MenuModule
    common/Profiles/simple_gatt
    common/Services/dev_info
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source"
)

target_link_libraries(target PRIVATE
    Device::cc23x0r5
    Drivers::drivers_cc23x0r5
    Drivers::display_cc23x0r5
    FreeRTOS::freertos
    Bluetooth::ble
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

    common/BLEAppUtil/bleapputil_init.c
    common/BLEAppUtil/bleapputil_process.c
    common/BLEAppUtil/bleapputil_stack_callbacks.c
    common/BLEAppUtil/bleapputil_task.c
    common/Drivers/NV/crc.c
    common/Drivers/NV/nvocmp.c
    common/iCall/icall_cc23x0.c
    common/iCall/icall_POSIX.c
    common/iCall/icall_user_config.c
    common/iCallBLE/ble_stack_api.c
    common/iCallBLE/ble_user_config.c
    common/iCallBLE/icall_api_lite.c
    common/MenuModule/menu_module.c
    common/Profiles/simple_gatt/simple_gatt_profile.c
    common/Services/dev_info/dev_info_service.c
    common/Startup/ble_user_config_stack.c
    common/Startup/osal_icall_ble.c
    common/Startup/rom_init.c
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
file(READ "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/ble5stack_flash/config/build_components.opt" FILE_CONTENTS)
string(REPLACE "-D" "" FILE_CONTENTS ${FILE_CONTENTS})
string(REPLACE "\n" ";" DEFINE_SYSCFG ${FILE_CONTENTS})

foreach(DEFINITION ${DEFINE_SYSCFG})
    add_compile_definitions(${DEFINITION})
endforeach()

# Add all defines from factory_config.opt
file(READ "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/ble5stack_flash/config/factory_config.opt" FILE_CONTENTS)
string(REPLACE "-D" "" FILE_CONTENTS ${FILE_CONTENTS})
string(REPLACE "\n" ";" DEFINE_SYSCFG ${FILE_CONTENTS})

foreach(DEFINITION ${DEFINE_SYSCFG})
    add_compile_definitions(${DEFINITION})
endforeach()

# Idealy, these would be built by reading the content of the .genmap file.
target_link_libraries(target PRIVATE
    #/* libraries required for /third_party/spiffs */
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/third_party/spiffs/lib/ticlang/m0p/spiffs.a"

    #/* libraries required for /ti/ble5stack */
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/ble5stack_flash/lib_projects/CC2340R5/OneLib/lib/ticlang/m0p/OneLib.a"
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/ble5stack_flash/lib_projects/CC2340R5/StackWrapper/lib/ticlang/m0p/StackWrapper.a"

    #/* libraries required for /ti/display */
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/display/lib/ticlang/m0p/display_cc23x0r5.a"

    #/* libraries required for /ti/drivers/rcl */
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/drivers/rcl/lib/ticlang/m0p/rcl_cc23x0r5.a"
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/devices/cc23x0r5/rf_patches/lib/ticlang/m0p/lrf_cc23x0r5.a"

    #/* libraries required for /ti/drivers */
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/drivers/lib/ticlang/m0p/drivers_cc23x0r5.a"

    #/* libraries required for /third_party/ecc */
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/third_party/ecc/lib/ticlang/m0p/ecc.a"

    #/* libraries required for /ti/devices */
    "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/ti/devices/cc23x0r5/driverlib/lib/ticlang/driverlib.a"
)

# Add all defines from ti_utils_build_linker.cmd.genlibs
#file(READ "${CMAKE_CURRENT_LIST_DIR}/syscfg/ti_utils_build_linker.cmd.genlibs" FILE_CONTENTS)
#string(REPLACE "\n" ";" LIBS_SYSCFG ${FILE_CONTENTS})

#foreach(LIB ${LIBS_SYSCFG})
#    message("${LIB}")
#    target_link_libraries(target PRIVATE "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/source/${LIB_PATH}")
#    add_compile_definitions(${LIB})
#endforeach()


target_link_options(target PRIVATE 
    "${CMAKE_CURRENT_LIST_DIR}/lpf3_app_freertos.cmd"
)

set(CRC_TOOL "${CMAKE_SOURCE_DIR}/external/simplelink-lowpower-f3-sdk-lpf3-8.40.02.01/tools/common/crc_tool/crc_tool")

add_custom_command(
    TARGET target
    POST_BUILD
    COMMAND ${CRC_TOOL} patch-image --elf "$<TARGET_FILE:target>" --output "$<TARGET_FILE:target>" --symbol-prefix ti_utils_build_GenMap_sym_CRC
)
