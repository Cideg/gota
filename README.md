# Gota Network


## About

Welcome to the repository of Gota. Here you will find source code, instructions, wiki resources, and integration tutorials.

Contents
* Building on Linux 64-bit
* Building on Mac OSX
* Building on Windows
* Building on other platforms

## Building on Linux 64-bit

All commands below work on Ubuntu 18.*, other distributions may need different command set.

### Building with standard options

Create directory `gota` somewhere and go there:
```
$> mkdir gota
$> cd gota
```

To go futher you have to have a number of packages and utilities. You need at least gcc 5.4.

* `build-essential` package:
    ```
    $gota> sudo apt-get install build-essential
    ```

* `libudev`:
    ```
    sudo apt-get install libudev-dev
    ```

* CMake (3.0 or newer):
    ```
    $gota> sudo apt-get install cmake
    $gota> cmake --version
    ```
    If version is too old, follow instructions on [the official site](https://cmake.org/download/).

* Boost (1.65 or newer):
    We use boost as a header-only library via find_boost package. So, if your system has boost installed and set up, it will be used automatically.

    Note - there is a bug in `boost::asio` 1.66 that affects `gotad`. Please use either version 1.65 or 1.67+.
    ```
    $gota> sudo apt-get install libboost-dev
    ```
    If the latest boost installed is too old (e.g. for Ubuntu 16.*), then you need to download and unpack boost into the `gota/boost` folder.

    ```
    $gota> wget -c 'https://dl.bintray.com/boostorg/release/1.69.0/source/boost_1_69_0.tar.gz'
    $gota> tar -xzf ./boost_1_69_0.tar.gz
    $gota> rm ./boost_1_69_0.tar.gz
    $gota> mv ./boost_1_69_0/ ./boost/
    ```

* OpenSSL (1.1.1 or newer):
    Install OpenSSL to `gota/openssl` folder. (In below commands use switch `linux-x86_64-clang` instead of `linux-x86_64` if using clang.)
    ```
    $gota> git clone --single-branch --branch OpenSSL_1_1_1b --depth 1 https://github.com/openssl/openssl.git
    $gota> cd openssl
    $gota/openssl> ./Configure linux-x86_64 no-shared
    $gota/openssl> make -j8
    $gota/openssl> cd ..
    ```

* LMDB
    Source files are referenced via relative paths, so you do not need to separately build it:
    Please note, we use LMDB only when building 64-bit daemons. For 32-bit daemons SQLite is used instead.

    Difference to official LMDB repository is lifted 2GB database limit if built by MSVC (even of 64-bit machine).
    ```
    $gota> git clone https://github.com/Gotanetwork/lmdb.git

    ```

Git-clone (or git-pull) Gota source code in that folder:
```
$gota> git clone https://github.com/Gotanetwork/gota.git
```

Create build directory inside gota, go there and run CMake and Make:
```
$gota> mkdir -p gota/build
$gota> cd gota/build
$gota/gota/build> cmake ..
$gota/gota/build> make -j8
```

Check built binaries by running them from `../bin` folder
```
$gota/gota/build> ../bin/gotad -v
```

## Building on Mac OSX

### Building with standard options (10.11 El Capitan or newer)

You need command-line tools. Either get XCode from an App Store or run 'xcode-select --install' in terminal and follow instructions. First of all, you need [Homebrew](https://brew.sh).

Then open terminal and install CMake and Boost:

* `brew install cmake`
* `brew install boost`

Create directory `gota` somewhere and go there:
```
$~/Downloads> mkdir gota
$~/Downloads> cd gota
```

Git-clone (or git-pull) Gota source code in that folder:
```
$gota> git clone https://github.com/Gotanetwork/gota.git
```

Put LMDB source code in `gota` folder (source files are referenced via relative paths, so you do not need to separately build it):
```
$~/Downloads/gota> git clone https://github.com/Gotanetwork/lmdb.git
```

Install OpenSSL to `gota/openssl` folder:
```
$~/Downloads/gota> git clone --single-branch --branch OpenSSL_1_1_1b --depth 1 https://github.com/openssl/openssl.git
$~/Downloads/gota> cd openssl
$~/Downloads/gota/openssl> ./Configure darwin64-x86_64-cc no-shared -mmacosx-version-min=10.11
$~/Downloads/gota/openssl> make -j8
$~/Downloads/gota/openssl> cd ..
```

Create build directory inside gota, go there and run CMake and Make:
```
$~/Downloads/gota> mkdir gota/build
$~/Downloads/gota> cd gota/build
$~/Downloads/gota/gota/build> cmake ..
$~/Downloads/gota/gota/build> make -j8
```

Check built binaries by running them from `../bin` folder:
```
$gota/gota/build> ../bin/gotad -v
```

## Building on Windows

You need Microsoft Visual Studio Community 2017. [Download](https://www.visualstudio.com/vs/) and install it selecting `C++`, `git`, `cmake integration` packages.
Run `Visual Studio x64 command prompt` from start menu.

Create directory `gota` somewhere:
```
$C:\> mkdir gota
$C:\> cd gota
```

Boost (1.65 or newer):
    We use boost as a header-only library via find_boost package. So, if your system has boost installed and set up, it will be used automatically. If not, you need to download and unpack boost into gota/boost folder.

Git-clone (or git-pull) Gota source code in that folder:
```
$C:\gota> git clone https://github.com/Gotanetwork/gota.git
```

Put LMDB in the same folder (source files are referenced via relative paths, so you do not need to separately build it):
```
$C:\gota> git clone https://github.com/Gotanetwork/lmdb.git
```

Download amalgamated [SQLite 3](https://www.sqlite.org/download.html) and unpack it into the same folder (source files are referenced via relative paths, so you do not need to separately build it).

You need to build openssl, first install ActivePerl (select "add to PATH" option, then restart console):
```
$C:\gota> git clone --single-branch --branch OpenSSL_1_1_1b --depth 1 https://github.com/openssl/openssl.git
$C:\gota> cd openssl
$C:\gota\openssl> perl Configure VC-WIN64A no-shared no-asm
$C:\gota\openssl> nmake
$C:\gota\openssl> cd ..
```
If you want to build 32-bit binaries, you will also need 32-bit build of openssl in separate folder (configuring openssl changes header files, so there is no way to have both 32-bit and 64-bit versions in the same folder):
```
$C:\gota> git clone --single-branch --branch OpenSSL_1_1_1b --depth 1 https://github.com/openssl/openssl.git openssl32
$C:\gota> cd openssl32
$C:\gota\openssl> perl Configure VC-WIN32 no-shared no-asm
$C:\gota\openssl> nmake
$C:\gota\openssl> cd ..
```

Now launch Visual Studio, in File menu select `Open Folder`, select `C:\gota\gota` folder.
Wait until CMake finishes running and `Build` appears in main menu.
Select `x64-Debug` or `x64-Release` from standard toolbar, and then `Build/Build Solution` from the main menu.

## Building with options

You can build daemons that use SQLite istead of LMDB on any platform by providing options to CMake.
You may need to clean 'build' folder, if you built with default options before, due to cmake aggressive caching.

```
$gota/build> cmake -DUSE_SQLITE=1 ..
$gota/build> time make -j8
```

## Building on 32-bit x86 platforms, iOS, Android and other ARM platforms

Gota works on 32-bit systems if SQLite is used instead of LMDB (we've experienced lots of problems building and running with lmdb in 32-bit compatibility mode, especially on iOS).

Building source code for iOS, Android, Raspberry PI, etc is possible (we have experimental `gotad` and `walletd` running on ARM64 iPhone) but requires major skills on your part. __TBD__

## Building on Big-Endian platforms

Currently gota does not work out of the box on any Big-Endian platform, due to some endianess-dependent code. This may be fixed in the future. If you wish to run on Big-Endian platform, please contact us.

## Building with parameters

If you want to use tools like `clang-tidy`, run `cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON ..` instead of `cmake ..`
