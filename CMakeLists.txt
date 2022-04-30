cmake_minimum_required(VERSION 2.8.12)
project(c2_fastbit C CXX)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)
    set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
            "Debug" "Release" "RelWithDebInfo" "MinSizeRel")
endif()
mark_as_advanced(CLEAR CMAKE_SKIP_RPATH CMAKE_EXE_LINKER_FLAGS)
set(BUILD_SHARED_LIBS "OFF" CACHE BOOL "Build a shared library")
# CMAKE_CXX_STANDARD is not defined until cmake 3.1, and will be ignored by
# cmake 3.0 and before.
set(FASTBIT_CXX_STANDARD "11" CACHE STRING "C++ std to probe")
set(FASTBIT_CXX_STANDARD_REQUIRED "OFF" CACHE BOOL "C++ std must be met")
set(FASTBIT_USE_CXX_ATOMIC "ON" CACHE BOOL "Use C++ <atomic>")
mark_as_advanced(FASTBIT_CXX_STANDARD FASTBIT_CXX_STANDARD_REQUIRED)
set_property(CACHE FASTBIT_CXX_STANDARD PROPERTY STRINGS "98" "11")
include(CheckCXXCompilerFlag)
if(CMAKE_VERSION VERSION_LESS "3.1")
    set(cxxstdflag "-std=c++${FASTBIT_CXX_STANDARD}")
    CHECK_CXX_COMPILER_FLAG(${cxxstdflag} flag${cxxstdflag})
    if(${flag${cxxstdflag}})
        add_compile_options(${cxxstdflag})
    else()
        if(FASTBIT_CXX_STANDARD_REQUIRED)
            message(FATAL_ERROR "Fail to enable CXX ${FASTBIT_CXX_STANDARD}")
        endif()
    endif()
else()
    set(CMAKE_CXX_STANDARD ${FASTBIT_CXX_STANDARD})
    set(CMAKE_CXX_STANDARD_REQUIRED ${FASTBIT_CXX_STANDARD_REQUIRED})
endif()

# link shared lib with full rpath. on some modern platforms, the newer
# runpath will be used instead of rpath.
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
# quiet CMP0042 warning  (not needed if cmake >=3.0)
set(CMAKE_MACOSX_RPATH 1)

add_subdirectory(src)
add_subdirectory(examples)
