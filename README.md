**Lawrence Berkeley National Laboratory's award-winning, high-performance, multidimensional data index engine**

[![CI](https://github.com/lanl-future-campaign/c2-fastbit/actions/workflows/ci.yml/badge.svg)](https://github.com/lanl-future-campaign/c2-fastbit/actions/workflows/ci.yml)
[![GitHub Release](https://img.shields.io/github/release/lanl-future-campaign/c2-fastbit.svg)](https://github.com/lanl-future-campaign/c2-fastbit/releases)
[![License](https://img.shields.io/badge/license-New%20BSD-blue.svg)](COPYING)

FastBit 2.0.3
================

```
XX              XXXXX XXX         XX XX           XX       XX XX XXX         XXX
XX             XXX XX XXXX        XX XX           XX       XX XX    XX     XX   XX
XX            XX   XX XX XX       XX XX           XX       XX XX      XX XX       XX
XX           XX    XX XX  XX      XX XX           XX       XX XX      XX XX       XX
XX          XX     XX XX   XX     XX XX           XX XXXXX XX XX      XX XX       XX
XX         XX      XX XX    XX    XX XX           XX       XX XX     XX  XX
XX        XX       XX XX     XX   XX XX           XX       XX XX    XX   XX
XX       XX XX XX XXX XX      XX  XX XX           XX XXXXX XX XX XXX     XX       XX
XX      XX         XX XX       XX XX XX           XX       XX XX         XX       XX
XX     XX          XX XX        X XX XX           XX       XX XX         XX       XX
XX    XX           XX XX          XX XX           XX       XX XX          XX     XX
XXXX XX            XX XX          XX XXXXXXXXXX   XX       XX XX            XXXXXX
```

**This is LANL's re-distribution of Lawrence Berkeley National Laboratory's FastBit 2.0.3 codebase with the build system switched from GNU autotools to cmake.** The original FastBit code is available at [code.lbl.gov](https://code.lbl.gov/projects/fastbit/). Visit [sdm.lbl.gov](https://sdm.lbl.gov/fastbit/) for more information regarding the technology, use cases, and documentations of the software. FastBit is open-source software. The original codebase uses a GNU autotools based build system. This re-distribution allows people to use cmake to more easily build the software especially for crosscompiling.

# Platform

FastBit is written in C++ and can be compiled and run both on Mac and on Linux platforms. C++11 (atomics) is not required to compile the code, but will be used if the compiler supports it. C++14 or later features are not used.

# Software requirements

Compiling FastBit requires g++, cmake, and make. On Ubuntu, one may use the following commands to prepare the programming environment for FastBit.

```bash
sudo apt-get install g++ make cmake cmake-curses-gui
```

For Ubuntu 20.04.4, this will install g++ 9.4.0, cmake 3.16.3, and make 4.2.1.

# Building

After the above step, we can proceed to build FastBit. Building FastBit requires us to determine an installation location where FastBit bin, include, lib, and share directories will be created and populated. The default location is `/usr/local`.

To build FastBit, create an out-of-source build directory and run 'ccmake' command from it:

```bash
git clone https://github.com/lanl-future-campaign/c2-fastbit.git
cd c2-fastbit
mkdir build
cd build
ccmake -DBUILD_SHARED_LIBS=ON -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=<installdir> ..
```

Type 'c' multiple times and choose suitable options. An example set of options are:

```bash
BUILD_SHARED_LIBS               *ON
CMAKE_BUILD_TYPE                *Release
CMAKE_EXE_LINKER_FLAGS          *
CMAKE_INSTALL_PREFIX            */tmp/usr/local
CMAKE_SKIP_RPATH                *OFF
FASTBIT_USE_CXX_ATOMIC          *ON
```

Type 'g' to generate build files and exit cmake. If 'g' is not available, type 'c' multiple times until 'g' appears. Once exiting cmake, we run `make` to build all targets.

```bash
make
```

**Note**: depending on the capabilities of the build machine, the make process may take a while to complete. Check our [github ci runs](https://github.com/lanl-future-campaign/c2-fastbit/actions/workflows/ci.yml) for examples of FastBit builds.

Example `ldd` output of `libfastbit.so` on Ubuntu 20.04.4:

```bash
ldd libfastbit.so
	linux-vdso.so.1 (0x00007ffce23b8000)
	libstdc++.so.6 => /lib/x86_64-linux-gnu/libstdc++.so.6 (0x00007fe53407b000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007fe533f2c000)
	libgcc_s.so.1 => /lib/x86_64-linux-gnu/libgcc_s.so.1 (0x00007fe533f11000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007fe533eee000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fe533cfc000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fe535b47000)
```

To install FastBit, run `make install` from the build directory.

# LLVM Clang, g++-11 and beyond

Compilation will fail when FastBit is compiled by a recent LLVM clang compiler or a g++-11 or later compiler. Errors such as `error: ordered comparison between pointer and zero ('unsigned short *' and 'int')` will be reported and will conclude the build process. Use a non-recent g++ compiler instead. On Mac, g++ can be installed via brew using `brew install gcc` (this will also install g++). To install a specific version of g++, use `brew install gcc@x`. FastBit will compile with gcc-6, gcc-7, gcc-8, gcc-9, and gcc-10 (all available under macOS Monterey). On Ubuntu, specific versions of g++ can be installed through `sudo apt-get install g++-x`. g++-6 is unavailable on Ubuntu 20.04.4 whereas g++-7 to g++-10 are all available. Ubuntu 20.04.4 installs g++-9 by default.

# Crosscompiling

One benefit of switching to cmake is an easier process for crosscompiling. In cases where an x86_64 Ubuntu host is used for crosscompiling an aarch64 target, use `sudo apt-get install g++-aarch64-linux-gnu` to obtain the corresponding g++ and do the following:

```bash
git clone https://github.com/lanl-future-campaign/c2-fastbit.git
cd c2-fastbit
mkdir build
cd build
CC=/usr/bin/aarch64-linux-gnu-gcc CXX=/usr/bin/aarch64-linux-gnu-g++ ccmake \
-DCMAKE_SYSTEM_NAME=Linux -DCMAKE_SYSTEM_PROCESSOR=aarch64 \
-DCMAKE_INSTALL_PREFIX=<installdir> \
-DCMAKE_BUILD_TYPE=Release \
-DCMAKE_SKIP_RPATH=ON \
-DCMAKE_EXE_LINKER_FLAGS=-static \
-DBUILD_SHARED_LIBS=OFF \
..
```

Do the same steps as described above and after `make` we will get an aarch64 libfastbit.so target. Note that when crosscompiling, we recommend to set `BUILD_SHARED_LIBS` to "OFF" and `CMAKE_EXE_LINKER_FLAGS` to "-static" to avoid issues where the glibc version of the host machine is different from that of the target. Glibc versions can be checked through `ldd --version`. Due to static linking, we also set `CMAKE_SKIP_RPATH` to `ON`. For non-Ubuntu hosts, one may obtain g++ from [ARM GNU toolchains](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain). The latest version is based on g++-11.2.
