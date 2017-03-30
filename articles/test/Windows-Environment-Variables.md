If you are building the Microsoft Cognitive Toolkit on your own machine, it requires the installation of several external components. The build process finds these installed components on your machine through environment variables. This allows you to install these components according to your wishes and preferences.

This page lists the environment variables which are used by the CNTK build process. It also lists the *preferred* location for these components. The preferred location is mirroring the configuration of our internal automated build and test machines. The preferred location is also the location used in the Wiki to describe the installation process. In the script in the script driven installation. But as was said before, this is only the *preferred* location, you can adjust it to your liking.

|Environment Variable | Preferred Location |   |
|:--------|:------------|:--------------|
|BOOST_INCLUDE_PATH | c:\local\boost_1_60_0-msvc-14.0 | [BOOST](./Setup-CNTK-on-Windows#boost) (required)
|BOOST_LIB_PATH | c:\local\boost_1_60_0-msvc-14.0\lib64-msvc-14.0 | [BOOST](./Setup-CNTK-on-Windows#boost) (required)
|CNTK_MKL_PATH | c:\local\CNTKCustomMKL | [Custom MKL for CNTK](.//Setup-CNTK-on-Windows#mkl) (required)
|CUB_PATH | c:\local\cub-1.4.1 | [NVIDIA CUB Library](.//Setup-CNTK-on-Windows#cub) (required for GPU build)
|CUDNN_PATH | C:\local\cudnn-8.0-v5.1\cuda | [NVIDIA cuDNN](./Setup-CNTK-on-Windows#cuDNN) (required for GPU build)
|CUDA_PATH | C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0 |[NVIDIA CUDA]( ./Setup-CNTK-on-Windows#nvidia-cuda) (required for GPU build)
|CUDA_PATH_V8_0 | C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0 |[NVIDIA CUDA]( ./Setup-CNTK-on-Windows#nvidia-cuda) (required for GPU build)
|OPENCV_PATH_V31 | c:\local\Opencv3.1.0\build | [OpenCV](./Setup-CNTK-on-Windows#optional-opencv) (required for ImagerReader build)
|PROTOBUF_PATH | c:\local\protobuf-3.1.0-vs15 | [Protobuf Library](./Setup-CNTK-on-Windows#protobuf) (required)
|SWIG_PATH | C:\local\swigwin-3.0.10 | [SWIG](./Setup-CNTK-on-Windows#swig) (required)
|ZLIB_PATH | c:\local\zlib-vs15 | [Compression Library](./Setup-CNTK-on-Windows#optional-zlib-and-libzip) (required for ImagerReader build)

In a command shell you could use these commands to set the environment variables on your machine:
```
setx  BOOST_INCLUDE_PATH c:\local\boost_1_60_0-msvc-14.0
setx  BOOST_LIB_PATH c:\local\boost_1_60_0-msvc-14.0\lib64-msvc-14.0
setx  CNTK_MKL_PATH c:\local\CNTKCustomMKL
setx  CUB_PATH c:\local\cub-1.4.1
setx  CUDNN_PATH C:\local\cudnn-8.0-v5.1\cuda
setx  OPENCV_PATH_V31 c:\local\Opencv3.1.0\build
setx  PROTOBUF_PATH c:\local\protobuf-3.1.0-vs15
setx  SWIG_PATH c:\local\swigwin-3.0.10
setx  ZLIB_PATH c:\local\zlib-vs15
```
The `CUDA_PATH` and `CUDA_PATH_V8_0` environment variables shouldn't be changed by you directly. The NVIDIA CUDA installer is defining these variables directly. 

There are several additional environment variables which can be used to define the CNTK features you build on your system. If you don't have these environment variables set on your system, the default value is assumed. 

|Environment Variable | Default value | |
|:------------|:------------|:-------------|
|CNTK_ENABLE_ASGD  | true | [Data Parallel ASGD](./Setup-CNTK-on-Windows#dataparallelasgd)
|CNTK_ENABLE_1BITSGD | false | [1bit SGD](./Enabling-1bit-SGD)
|CNTK_PY27_PATH |  | [Path to the CNTK Python 2.7 environment](./Setup-CNTK-on-Windows#cntk-python-environments)
|CNTK_PY34_PATH |  | [Path to the CNTK Python 3.4 environment](./Setup-CNTK-on-Windows#cntk-python-environments)
|CNTK_PY35_PATH |  | [Path to the CNTK Python 3.5 environment](./Setup-CNTK-on-Windows#cntk-python-environments)

Assuming the defaul values:
 * DataParallel ASGD *is* being built
 * 1bit SGD *is not* being built
 * With undefined `CNTK_PY.._PATH`, Python libraries are *not* being built
 
  
**More information**
* [Setup CNTK on your machine](./Setup-CNTK-on-your-machine)
