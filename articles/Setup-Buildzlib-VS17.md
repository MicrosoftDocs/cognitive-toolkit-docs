---
title:   Setup Buildzlib VS17
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   01/25/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---
# Setup Buildzlib VS17

## Creating a zlib/libzip compression library for CNTK

We use the following installation paths in our instructions:

* libzip sources: `c:\local\src\libzip-1.1.3`
* zlib sources: `c:\local\src\zlib\zlib-1.2.8`
* Build directory for zlib and libzip binaries: `c:\local\zlib-vs17`

You can use any paths you like, but note the remark on target folder for binaries below.

* Install [CMake](https://cmake.org/download/). Include CMake binaries folder to your `PATH`
* Download [libzip source code](http://nih.at/libzip/) and unpack to `c:\local\src\libzip-1.1.3`
* Download [zlib source code](http://zlib.net/) and unpack it to `c:\local\src\zlib\zlib-1.2.8`

There are two ways to build the lib/libzip compression library for CNTK, either through ***a batch file*** or ***manually***.

### Building through a batch file

In the directory ```Tools\devInstall\Windows``` you find the batch file ```buildZlibVS17.bat```. This batch file takes three parameters to build the CNTK Protobuf library. The first parameter is the directory of the ```libzip``` source files you unpacked above, the second parameter is the directory of the ```zlib``` source files,the third parameter is the destination directory for the created library. In our case follow these steps from a standard Windows command prompt:

```
c:
cd \repos\cntk\Tools\devInstall\Windows
buildZlibVS17 c:\local\src\libzip-1.1.3 c:\local\src\zlib\zlib-1.2.8 c:\local\zlib-vs17
```

Set the environment variable `ZLIB_PATH` to the location of the created library (this is the third parameter in the command invocation above, not the sources location!

```
setx ZLIB_PATH c:\local\zlib-vs17
```

### Manual build steps for a CNTK compression library

The following aspects are important:

* Build commands for zlib and libzip should use *the same* target folder for binaries (specified via `-DCMAKE_INSTALL_PREFIX` parameter in `cmake` command below)
* Build order is important. You should *build zlib first* because libzip is dependent on it

### Command prompt environment for build

> [!NOTE]
> If `VS2017INSTALLDIR` environment variable was not set by the Visual Studio installer, set it manually. The exact installation directory depends on both the *version* (2017 in this case) and *offering* (*Community*, *Enterprise*, etc) of Visual Studio, according to the following pattern: `C:\Program Files (x86)\Microsoft Visual Studio\<version>\<offering>`.
>
> For *Visual Studio 2017 Enterprise* edition, run:
> ```
> setx VS2017INSTALLDIR "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise"
> ```
> For *Visual Studio 2017 Community* edition, run:
> ```
> setx VS2017INSTALLDIR "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community"
> ```

Launch a 64-bit Visual Studio 2017 development environment. The most convenient way is to execute the batch file `vcvarsall.bat` in the Visual Studio directory with the required parameters from a standard windows command shell:

```
    "%VS2017INSTALLDIR%\VC\Auxiliary\Build\vcvarsall.bat" amd64 --vcvars_ver=14.11
```

If you can't find this batch file on your system, please revisit the installation of VS2017, and make sure you have the 'VC++ version 15.4 v14.11 toolset' option selected.

### Building zlib

Execute the following commands from *VS2017 x64 Native Tools Command Prompt*:

```
cd c:\local\src\zlib\zlib-1.2.8
mkdir build
cd build
cmake .. -G"Visual Studio 15" -DCMAKE_INSTALL_PREFIX="c:\local\zlib-vs17"
msbuild /P:Configuration=Release INSTALL.vcxproj
```

### Building libzip

Execute the following commands from *VS2017 x64 Native Tools Command Prompt*:

```
cd c:\local\src\libzip-1.1.3
mkdir build
cd build
cmake .. -G"Visual Studio 15 Win64" -DCMAKE_INSTALL_PREFIX="c:\local\zlib-vs17"
msbuild libzip.sln /t:zip /P:Configuration=Release
cmake -DBUILD_TYPE=Release -P cmake_install.cmake
```

MSBuild command above may generate some warnings. It is expected and may be ignored.

The last `cmake` command from the list above will generate the following error:

```
CMake Error at src/cmake_install.cmake:34 (file):
  file INSTALL cannot find "C:/libzip/build/src/Release/zipcmp.exe".
```

It is expected and may be ignored - all required binaries (`zip.dll` and `zlib.dll`) should be already in `c:\local\zlib-vs17`

### Preparing the environment

Set the following environment variable:

```
setx ZLIB_PATH c:\local\zlib-vs17
```
