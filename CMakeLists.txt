cmake_minimum_required(VERSION 3.15)

if(POLICY CMP0194)
    cmake_policy(SET CMP0194 NEW)
endif()

if(MIPS32_BUILD)
    project(KotukuAero VERSION 1.0.0 LANGUAGES C CXX)
else()
    project(KotukuAero VERSION 1.0.0 LANGUAGES C CXX)
endif()

# Set default build type if not specified
if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Build type" FORCE)
endif()

set(DIYEFIS_SOURCE_DIR ${CMAKE_SOURCE_DIR}/diy-efis)

if(MIPS32_BUILD)
    # ============================================================================
    # MIPS32 build  (cmake -DCMAKE_TOOLCHAIN_FILE=/diy-efis/cmake/mips32-toolchain.cmake ...)
    # ============================================================================

    message(STATUS "")
    message(STATUS "========================================")
    message(STATUS "MIPS32 Build")
    message(STATUS "  Build type: ${CMAKE_BUILD_TYPE}")
    message(STATUS "========================================")

    add_subdirectory(diy-efis/src/canfly)
    add_subdirectory(diy-efis/src/mfdlib)
    add_subdirectory(diy-efis/src/proton)

    add_subdirectory(diy-efis/apps/aio)
    add_subdirectory(diy-efis/apps/att)
    add_subdirectory(diy-efis/apps/dial)
    add_subdirectory(diy-efis/apps/edd)
    add_subdirectory(diy-efis/apps/hsi)
    add_subdirectory(diy-efis/apps/nav)
    add_subdirectory(diy-efis/apps/pfd)
    add_subdirectory(diy-efis/apps/setup)
else()
# ============================================================================
# Host build - WIN32 or native GCC/Clang
# (tools, diy-efis emulator, tests)
# ============================================================================
    if(MSVC)
        set(CMAKE_WIN32_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
    else()
        # Linux build: Force 32-bit compilation to emulate PIC32 architecture
        # The emulator assumes 32-bit semantics throughout the codebase
        set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -m32")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -m32")
        set(CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -m32")
        set(CMAKE_SHARED_LINKER_FLAGS "${CMAKE_SHARED_LINKER_FLAGS} -m32")
        set(CMAKE_MODULE_LINKER_FLAGS "${CMAKE_MODULE_LINKER_FLAGS} -m32")

        # Enable Position Independent Code for all libraries
        # This is required for shared libraries and prevents DT_TEXTREL warnings
        set(CMAKE_POSITION_INDEPENDENT_CODE ON)
    endif()

    ############################################################################
    # diy-efis - DESKTOP only (GUI emulator / user-mode MIPS development)
    ############################################################################
    add_subdirectory(diy-efis/tools/yehudi)
    add_subdirectory(diy-efis/src/mfdlib)
    add_subdirectory(diy-efis/src/proton)
    add_subdirectory(diy-efis/apps/aio)
    add_subdirectory(diy-efis/apps/att)
    add_subdirectory(diy-efis/apps/edd)
    add_subdirectory(diy-efis/apps/dial)
    add_subdirectory(diy-efis/apps/hsi)
    add_subdirectory(diy-efis/apps/nav)
    add_subdirectory(diy-efis/apps/pfd)
    add_subdirectory(diy-efis/apps/setup)
endif()
