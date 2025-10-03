# Copyright (c) 2022-2023, Texas Instruments Incorporated
# All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
# *  Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
#
# *  Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
#
# *  Neither the name of Texas Instruments Incorporated nor the names of
#    its contributors may be used to endorse or promote products derived
#    from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO,
# THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
# PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT OWNER OR
# CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
# EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
# PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
# OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
# WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR
# OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE,
# EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
cmake_minimum_required(VERSION ${TI_MIN_CMAKE_VERSION})

# Set BLUETOOTH_INSTALL_DIR to SDK root if it is not explicitly defined
get_install_dir(BLUETOOTH_INSTALL_DIR)

ti_init_package(NAMESPACE Bluetooth)

# Add a top-level BLE library
ti_add_library(ble INTERFACE ARCH all)
target_include_directories(
    ble
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/controller/cc26xx/inc>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/inc>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/rom>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/common/cc26xx>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/icall/inc>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/hal/src/target/_common>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/common/cc26xx/npi/stack>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/hal/src/inc>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/heapmgr>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/profiles/dev_info>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/profiles/simple_profile>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/icall/src/inc>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/npi/src>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/osal/src/inc>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/services/src/saddr>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/services/src/sdata>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/common/nv>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/common/cc26xx>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/ble5stack_flash/icall/src>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/bleapp/profiles/health_thermometer>"
    INTERFACE "$<BUILD_INTERFACE:${BLUETOOTH_INSTALL_DIR}/source/ti/bleapp/services/health_thermometer>"
              # Note: we cannot set the INSTALL_INTERFACE include here, because it depends on
              # BLUETOOTH_INSTALL_DIR It is configured in ConfigFreeRTOS.cmake.in, after the CMake
              # package magic
)

add_dependencies(ble drivers_cc23x0r5)

# Hardcode them since they are not built anyway

ti_export_package()
