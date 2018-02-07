---
title:   Windows Environment Variables
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---
# Windows Environment Variables

If you are building the Microsoft Cognitive Toolkit on your own machine, it requires the installation of several external components. The build process finds these installed components on your machine through environment variables. This allows you to install these components according to your wishes and preferences.

This page lists the environment variables which are used by the CNTK build process. It also lists the *preferred* location for these components. The preferred location is mirroring the configuration of our internal automated build and test machines. The preferred location is also the location used in the documentation to describe the installation process. As was said before, this is only the *preferred* location and you can adjust it to your liking.

|Environment Variable | Preferred Location |   |
|:--------|:------------|:--------------|
|BOOST_INCLUDE_PATH | c:\local\boost_1_60_0-msvc-14.0 | [BOOST](./Setup-CNTK-on-Windows.md#boost) (required)
|BOOST_LIB_PATH | c:\local\boost_1_60_0-msvc-14.0\lib64-msvc-14.0 | [BOOST](./Setup-CNTK-on-Windows.md#boost) (required)
|CNTK_OPENBLAS_PATH | c:\local\CNTKopenBLAS | [OpenBLAS library for CNTK UWP build](./Setup-UWP-Build-on-Windows.md) (optional)
|CUB_PATH | c:\local\cub-1.7.4 | [NVIDIA CUB Library](./Setup-CNTK-on-Windows.md#cub) (required for GPU build)
|CUDNN_PATH | C:\local\cudnn-9.0-v7.0\cuda | [NVIDIA cuDNN](./Setup-CNTK-on-Windows.md#cudnn) (required for GPU build)
|CUDA_PATH | C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0 |[NVIDIA CUDA]( ./Setup-CNTK-on-Windows.md#nvidia-cuda-90) (required for GPU build)
|CUDA_PATH_V9_0 | C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0 |[NVIDIA CUDA]( ./Setup-CNTK-on-Windows.md#nvidia-cuda-90) (required for GPU build)
|MKL_PATH | C:\local\mklml-mkldnn-0.12 |[MKL_PATH]( ./Setup-CNTK-on-Windows.md#mkl) (required)
|OPENCV_PATH_V31 | c:\local\Opencv3.1.0\build | [OpenCV](./Setup-CNTK-on-Windows.md#optional-opencv) (required for ImagerReader build and TensorBoard's Image feature)
|PROTOBUF_PATH | c:\local\protobuf-3.1.0-vs17 | [Protobuf Library](./Setup-CNTK-on-Windows.md#protobuf) (required)
|SWIG_PATH | C:\local\swigwin-3.0.10 | [SWIG](./Setup-CNTK-on-Windows.md#optional-swig) (required for CNTK Python, Java, and C#/.NET support)
|ZLIB_PATH | c:\local\zlib-vs17 | [Compression Library](./Setup-CNTK-on-Windows.md#optional-zlib-and-libzip) (required for ImagerReader build)
|VS2017INSTALLDIR | C:\Program Files (x86)\Microsoft Visual Studio\\<code>version</code>\\<code>offering</code> | [CNTK on Windows](./Setup-CNTK-on-Windows.md#building-cntk) (required for building CNTK 2.4+ with VS2017)

In a command shell you could use these commands to set the environment variables on your machine:
```
setx  BOOST_INCLUDE_PATH c:\local\boost_1_60_0-msvc-14.0
setx  BOOST_LIB_PATH c:\local\boost_1_60_0-msvc-14.0\lib64-msvc-14.0
setx  CUB_PATH c:\local\cub-1.7.4
setx  CUDNN_PATH C:\local\cudnn-9.0-v7.0\cuda
setx  MKL_PATH C:\local\mklml-mkldnn-0.12
setx  OPENCV_PATH_V31 c:\local\Opencv3.1.0\build
setx  PROTOBUF_PATH c:\local\protobuf-3.1.0-vs17
setx  SWIG_PATH c:\local\swigwin-3.0.10
setx  ZLIB_PATH c:\local\zlib-vs17
```
The `CUDA_PATH` and `CUDA_PATH_V9_0` environment variables shouldn't be changed by you directly. The NVIDIA CUDA installer is defining these variables directly. 

There are several additional environment variables which can be used to define the CNTK features you build on your system. If you don't have these environment variables set on your system, the default value is assumed. 

|Environment Variable | Default value | |
|:------------|:------------|:-------------|
|CNTK_ENABLE_ASGD  | true | [Data Parallel ASGD](./Setup-CNTK-on-Windows.md#enlisting-in-the-cntk-github-repository)
|CNTK_ENABLE_1BITSGD | false | [1bit SGD](./Enabling-1bit-SGD.md)
|CNTK_PY27_PATH |  | [Path to the CNTK Python 2.7 environment](./Setup-CNTK-on-Windows.md#cntk-python-environments)
|CNTK_PY35_PATH |  | [Path to the CNTK Python 3.5 environment](./Setup-CNTK-on-Windows.md#cntk-python-environments)
|CNTK_PY36_PATH |  | [Path to the CNTK Python 3.6 environment](./Setup-CNTK-on-Windows.md#cntk-python-environments)

Assuming the default values:
 * DataParallel ASGD *is* being built
 * 1bit SGD *is not* being built
 * With undefined `CNTK_PY.._PATH`, Python libraries are *not* being built

# Additional Environment Variables

There are additional environment variables which can influence the compilation process:

| Environment Variables | |
|:------------|:-------------|
|CNTK_CUDA_CODEGEN_DEBUG CNTK_CUDA_CODEGEN_RELEASE | With these environment variables you can define the NVidia Compiler target architectures. For example, setting a variable to `compute_35,sm_35;compute_50,sm_50` will only build level 3.5 and 5.0 compatible cubin and PTX information. For detailed information about this refer to the NVidia Compiler documentation.

**More information**

* [Setup CNTK on your machine](./Setup-CNTK-on-your-machine.md)
