cmake_minimum_required(VERSION 3.24)

project(ti-sdk-as-subdirectory VERSION 0.1.0)

add_subdirectory(external)

if(${TI_TOOLCHAIN_NAME} STREQUAL "ticlang")
	add_subdirectory(target_ticlang)
endif()

if(${TI_TOOLCHAIN_NAME} STREQUAL "gcc")
	add_subdirectory(target_gcc)
endif()
