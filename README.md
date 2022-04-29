**Lawrence Berkeley National Laboratory's award-winning, high-performance, multidimensional data index engine**

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

FastBit is written in c++ and can be compiled and run both on Mac and on Linux platforms. C++11 (atomics) is not required to compile the code, but will be used if the compiler supports it. C++14 or later features are not used.

# Software requirements

Compiling FastBit requires gcc/g++, cmake, and make. On Ubuntu, you may use the following commands to prepare the programming environment for FastBit.

```bash
sudo apt-get install gcc g++ make cmake cmake-curses-gui
```

For Ubuntu 20.04.4, this will install gcc/g++ 9.4.0, cmake 3.16.3, and make 4.2.1.

# Building

After the above step, we can proceed to build FastBit. To do that, create an out-of-source build directory and run 'ccmake' command from it:

```bash
git clone https://github.com/lanl-future-campaign/c2-fastbit.git
cd c2-fastbit
mkdir build
cd build
ccmake -DBUILD_SHARED_LIBS=ON -DCMAKE_BUILD_TYPE=RelWithDebInfo ..
```

Type 'c' multiple times and choose suitable options. An example set of options are:

```bash
BUILD_SHARED_LIBS               *ON
CMAKE_BUILD_TYPE                *RelWithDebInfo
CMAKE_INSTALL_PREFIX            */usr/local
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

# LLVM Clang, g++-11 and beyond

Compiling will fail if FastBit is compiled by a recent LLVM clang compiler or a g++-11 or later compiler. Errors such as `error: ordered comparison between pointer and zero ('unsigned short *' and 'int')` will be reported and will fail the build process. Use a non-recent gcc/g++ compiler instead. On Mac, gcc/g++ may be installed via brew using `brew install gcc`. To install a specific version of gcc, use `brew install gcc@x`. FastBit will compile with `gcc-6`, `gcc-7`, `gcc-8`, `gcc-9`, and `gcc-10`.
