---
title:   Setup BuildMklDnn VS17
author:    KeDengMS
ms.author:   kedeng
ms.date:   01/22/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Setup BuildMklDnn VS 2017

## Creating a compiled MKL-DNN 0.12 for CNTK

If you want to build the MKL-DNN 0.12 for CNTK yourself, follow these instructions. The compiled MKL-DNN version will be located in `c:\local\mklml-mkldnn-0.12`

* Install CMake from [cmake.org](https://cmake.org/download/).
* Download and unpack the MKLML binary from https://github.com/01org/mkl-dnn/releases/download/v0.12/mklml_win_2018.0.1.20171227.zip to `c:\local\mklml-mkldnn-0.12`
* Download and unpack the MKL-DNN sources from https://github.com/01org/mkl-dnn/archive/v0.12.zip.

The following steps assume that the MKL-DNN sources have been unzipped to the directory `c:\local\src\mkl-dnn-0.12`. The instructions below will create the MKL-DNN libraries and header files in the directory `c:\local\mklml-mkldnn-0.12`. If this location differs in your case, please adapt the steps below to match your directory structure.

Note that MKL-DNN headers and libraries are stored in the same location as MKLML to simpilify setup, since their file names are different. mkl_dnn* files are for dnn functions in MKL which is not open source, while mkldnn* files are built from open source MKL-DNN.

There are two ways to build the CNTK MKL-DNN library, either through ***a batch file*** or ***manually***.

### Building through a batch file

In the directory `Tools\devInstall\Windows` you find the batch file `buildMklDnnVS17.bat`. This batch file takes two parameters to build the CNTK MKL-DNN library. The first parameter is the directory of the MKL-DNN source files you unpacked above, the second parameter is the destination directory for the created MKL-DNN library. In our case follow these steps from a standard Windows command prompt:
```
c:
cd \repos\cntk\Tools\devInstall\Windows
buildMklDnnVS17 C:\local\src\mkl-dnn-0.12 C:\local\mklml-mkldnn-0.12
```
Set the environment variable `MKL_PATH` to the MKL-DNN location (this is the second parameter in the command invocation above, not the sources location!
```
setx MKL_PATH c:\local\mklml-mkldnn-0.12
```

### Manual build steps for a CNTK MKL-DNN library

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

1. Launch a 64-bit Visual Studio 2017 development environment. The most convenient way is to execute the batch file `vcvarsall.bat` in the Visual Studio directory with the required parameters from a standard windows command shell:
```
"%VS2017INSTALLDIR%\VC\Auxiliary\Build\vcvarsall.bat" amd64 --vcvars_ver=14.11
```
If you can't find this batch file on your system, please revisit the installation of VS2017 and make sure you have the 'VC++ version 15.4 v14.11 toolset' option selected.
2. Create a build directory, and set MKLROOT for MKL-DNN cmake to locate MKLML library:
```
cd c:\local\src\mkl-dnn-0.12
set MKLROOT=c:\local\mklml-mkldnn-0.12
```
3. Create a release build using CMake, and copy built binary to MKL_PATH (user may replace release with debug to create a debug build):
```
cd cmake
cmake .. -G "Visual Studio 15" -DCMAKE_BUILD_TYPE=Release
msbuild "Intel(R) MKL-DNN.sln" /t:Rebuild /p:Configuration=Release /m
copy ..\include\* c:\local\mklml-mkldnn-0.12\include
copy src\Release\*.lib c:\local\mklml-mkldnn-0.12\lib
copy src\Release\*.dll c:\local\mklml-mkldnn-0.12\lib
```
