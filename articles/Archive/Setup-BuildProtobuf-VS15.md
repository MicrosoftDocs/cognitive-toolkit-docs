---
title:   Setup BuildProtobuf VS15
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   01/25/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   NA
---

# Setup BuildProtobuf VS15

## Creating a compiled Protobuf 3.1.0 for CNTK

If you want to build the Protobuf 3.1.0 for CNTK yourself, follow these instructions. The compiled Protobuf version will be located in `c:\local\protobuf-3.1.0-vs15`

* Install CMake from [cmake.org](https://cmake.org/download/).
* Download and unpack the protobuf sources from https://github.com/google/protobuf/archive/v3.1.0.zip.

The following steps assume that the Protobuf sources have been unzipped to the directory `c:\local\src\protobuf-3.1.0`. The instructions below will create the Protobuf libraries and header files in the directory `c:\local\protobuf-3.1.0-vs15`. If this location differs in your case, please adapt the steps below to match your directory structure.

There are two ways to build the CNTK Protobuf library, either through ***a batch file*** or ***manually***.

### Building through a batch file

In the directory `Tools\devInstall\Windows` you find the batch file `buildProtoVS15.bat`. This batch file takes two parameters to build the CNTK Protobuf library. The first parameter is the directory of the Protobuf source files you unpacked above, the second parameter is the destination directory for the created Protobuf library. In our case follow these steps from a standard Windows command prompt:
```
c:
cd \repos\cntk\Tools\devInstall\Windows
buildProtoVS15 C:\local\src\protobuf-3.1.0 C:\local\protobuf-3.1.0-vs15
```
Set the environment variable `PROTOBUF_PATH` to the Protobuf location (this is the second parameter in the command invocation above, not the sources location!
```
    setx PROTOBUF_PATH c:\local\protobuf-3.1.0-vs15
```

### Manual build steps for a CNTK Protobuf library

1. Launch a 64-bit Visual Studio 2015 development environment. The most convenient was is to execute the batch file `vcvarsall.bat` in the Visual Studio directory with the required parameters from a standard windows command shell:
```
    "C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" amd64
```
If you can't find this batch file on your system, please revisit the installation of VS2015 and make sure you have the 'Common Tools for Visual C++ 2015' option selected.
2. Create a build directory:  
```
    c:\users\users> cd C:\local\src\protobuf-3.1.0\cmake
    C:\local\src\protobuf-3.1.0\cmake> mkdir build && cd build
```
3. Create a debug build: 
```
    C:\local\src\protobuf-3.1.0\cmake\build>mkdir debug && cd debug
    C:\local\src\protobuf-3.1.0\cmake\build\debug>cmake -G "NMake Makefiles" -DCMAKE_BUILD_TYPE=Debug -Dprotobuf_BUILD_TESTS=OFF -Dprotobuf_MSVC_STATIC_RUNTIME=OFF -DCMAKE_INSTALL_PREFIX="c:\local\protobuf-3.1.0-vs15" ..\..
    C:\local\src\protobuf-3.1.0\cmake\build\debug>nmake && nmake install
    C:\local\src\protobuf-3.1.0\cmake\build\debug>cd ..
```
4. Create a release build:
```
    C:\local\src\protobuf-3.1.0\cmake\build>mkdir release && cd release
    C:\local\src\protobuf-3.1.0\cmake\build\release>cmake -G "NMake Makefiles" -DCMAKE_BUILD_TYPE=Release -Dprotobuf_BUILD_TESTS=OFF -Dprotobuf_MSVC_STATIC_RUNTIME=OFF -DCMAKE_INSTALL_PREFIX="c:\local\protobuf-3.1.0-vs15" ..\..
    C:\local\src\protobuf-3.1.0\cmake\build\release>nmake && nmake install
    C:\local\src\protobuf-3.1.0\cmake\build\debug>cd ..
```
5. Set the environment variable `PROTOBUF_PATH` to the Protobuf location (Note: this is the install location used in the cmake command above, not the sources location!):
```
    setx PROTOBUF_PATH c:\local\protobuf-3.1.0-vs15
```
