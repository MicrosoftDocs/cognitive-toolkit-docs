---
title:   Setup Windows Python
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/23/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   python
---

# Setup Windows Python

## Installing CNTK for Python on Windows

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK) to use from Python in Windows. If you are looking for any other kind of support to setup a CNTK build environment or installing CNTK on your system, you should go [here](./Setup-CNTK-on-your-machine.md) instead. 

We offer three ways to install CNTK for Python:
1. [PyPI installation](#1-install-from-pypi)
2. [Wheel (.whl) files for each release](#2-install-from-wheel-files)
3. [Nightly builds](#3-install-from-nightly-builds)

### 1. Install from PyPI
As of the CNTK 2.5 release, users can now install CNTK via PyPI.  

If this is your first time installing CNTK via PyPI, we advise that you first uninstall any previous versions:
`pip uninstall <url>`.

#### First time CNTK installation
To install the CPU-only version of CNTK:
```
C:\> pip install cntk
```

To install the GPU version of CNTK:
```
C:\> pip install cntk-gpu
```
#### Upgrade an existing CNTK installation
If you already have a previous version (2.5+) of CNTK installed, you can install a new version of CNTK over your existing installation.  

To upgrade the CPU-only version of CNTK:
```
C:\> pip install --upgrade --no-deps cntk
```

To upgrade the GPU version of CNTK:
```
C:\> pip install --upgrade --no-deps cntk-gpu
```

**Note:** we advise that you do not have both `cntk` and `cntk-gpu` packages installed simultaneously.

### 2. Install from Wheel Files
Depending on the Python and CNTK version (CPU or GPU) we supply different wheel (.whl) files to install CNTK. Please select the correct installation from the list below, and substitute the name and/or link during the installation. For CNTK 2.5+, we recommend you simply install through [PyPI](#installfrompypi) instead.

* Easy pip install for [Anaconda3 4.1.1](#anaconda3)
* Easy pip install for [Anaconda2 4.3.0.1](#anaconda2)

# [CNTK 2.5.1](#tab/cntkpy251)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5.1-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp27-cp27m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5.1-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5.1-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5.1-cp36-cp36m-win_amd64.whl`

# [CNTK 2.5](#tab/cntkpy25)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5-cp27-cp27m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk_gpu-2.5-cp36-cp36m-win_amd64.whl`

# [CNTK 2.4](#tab/cntkpy24)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.4-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.4-cp27-cp27m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.4-cp27-cp27m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.4-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.4-cp35-cp35m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.4-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.4-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.4-cp36-cp36m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.4-cp36-cp36m-win_amd64.whl`

# [CNTK 2.3.1](#tab/cntkpy231)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3.1-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3.1-cp27-cp27m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3.1-cp27-cp27m-win_amd64.whl`
| 3.4 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3.1-cp34-cp34m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3.1-cp34-cp34m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3.1-cp34-cp34m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3.1-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3.1-cp35-cp35m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3.1-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3.1-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3.1-cp36-cp36m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3.1-cp36-cp36m-win_amd64.whl`

# [CNTK 2.3](#tab/cntkpy23)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3-cp27-cp27m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3-cp27-cp27m-win_amd64.whl`
| 3.4 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3-cp34-cp34m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3-cp34-cp34m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3-cp34-cp34m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3-cp35-cp35m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.3-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.3-cp36-cp36m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.3-cp36-cp36m-win_amd64.whl`

# [CNTK 2.2](#tab/cntkpy22)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.2-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp27-cp27m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.2-cp27-cp27m-win_amd64.whl`
| 3.4 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.2-cp34-cp34m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp34-cp34m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.2-cp34-cp34m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.2-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp35-cp35m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.2-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.2-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.2-cp36-cp36m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.2-cp36-cp36m-win_amd64.whl`

# [CNTK 2.1](#tab/cntkpy21)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp27-cp27m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp27-cp27m-win_amd64.whl`
| 3.4 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp34-cp34m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp34-cp34m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp34-cp34m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp35-cp35m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.1-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.1-cp36-cp36m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.1-cp36-cp36m-win_amd64.whl`

# [CNTK 2.0](#tab/cntkpy20)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp27-cp27m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp27-cp27m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp27-cp27m-win_amd64.whl`
| 3.4 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp34-cp34m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp34-cp34m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp34-cp34m-win_amd64.whl`
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp35-cp35m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp35-cp35m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp35-cp35m-win_amd64.whl`
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp36-cp36m-win_amd64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp36-cp36m-win_amd64.whl`
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp36-cp36m-win_amd64.whl`

---

#### Anaconda3

We have been testing CNTK with Anaconda3 4.1.1 (64-bit) and Python versions 2.7 and 3.5, as well as Anaconda3 4.3.1 with Python version 3.6. If you do not have a Anaconda3 Python installation, install [Anaconda3 4.1.1 Python for Windows (64-bit)](https://repo.continuum.io/archive/Anaconda3-4.1.1-Windows-x86_64.exe).

Below we assume Anaconda is installed and that it is listed before any other Python installations in your PATH. If you plan on using a GPU-enabled version of CNTK, you will need a CUDA 9 compliant graphics card and up-to-date graphics drivers installed on your system. Make sure you are installing CUDA 9.0 and not CUDA 9.1.

##### pip install without an environment

This is the easiest option and the only reason to avoid it is if you require specific versions of certain packages. If you have other packages that require an old version of numpy skip to [this section](#pip-install-in-an-environment). 

###### First time CNTK installation

If this is the first time you install CNTK then run
```
C:\> pip install <url>
```
where `<url>` is the corresponding wheel file URL in the table on the top of this page. E.g. if you have Python 3.5 and want to install the CPU-only version, run
```
C:\> pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5.1-cp35-cp35m-win_amd64.whl
```

Continue with a [quick installation test](#quick-installation-test)

###### Upgrade an existing CNTK installation

If you already have a previous version of CNTK installed, you can install a new version of CNTK over your existing installation. It is important to supply the `--upgrade` and `--no-deps` options.
```
C:\> pip install --upgrade --no-deps <url>
``` 
where `<url>` is the corresponding wheel file URL in the table on the top of this page. Once you have completed this upgrade step, you can start working with CNTK in Python or [install samples and tutorials](#installing-samples-and-tutorials).

###### Quick installation test

A quick test that the installation succeeded can be done by querying the CNTK version:
```
C:\> python -c "import cntk; print(cntk.__version__)"
```

You now have successfully installed CNTK, and you can start developing / training / evaluating with CNTK in Python!

Continue with [installing samples and tutorials](#installing-samples-and-tutorials)

##### pip install in an environment

Below we will create a new Python 3.5 environment inside Anaconda called `cntk-py35` and will pip-install CNTK into this environment. If you want a different CNTK version, Python version, or a different environment name, adjust the parameters accordingly.

Open a standard command shell, create the environment, make it active, and pip-install CNTK:
```
C:\> conda create --name cntk-py35 python=3.5 numpy scipy h5py jupyter
C:\> activate cntk-py35
C:\> pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.5.1-cp35-cp35m-win_amd64.whl
```
A quick test that the installation succeeded can be done by querying the CNTK version:
```
C:\> python -c "import cntk; print(cntk.__version__)"
```

You now have successfully installed CNTK, and you can start developing / training / evaluating with CNTK in Python!

Continue with a [installing samples and tutorials](#installing-samples-and-tutorials)

#### Anaconda2 

If you require a Python 2.7 root environment, we recommend you install [Anaconda2 4.3.0.1 (64-bit)](https://repo.continuum.io/archive/Anaconda2-4.3.0.1-Windows-x86_64.exe).

Below we assume Anaconda2 is installed and that it is listed before any other Python installations in your PATH. If you plan on using a GPU enabled version of CNTK, you will need a CUDA 9 compliant graphics card and up-to-date graphics drivers installed on your system.

##### Anaconda2: CNTK prerequisites

CNTK requires the `Visual C++ Redistributable for Visual Studio 2017` installed on your system (in many cases this will already be the case).
The installer for the VS2017 Runtime (`VC_redist.x64.exe`) can be downloaded [here](https://aka.ms/vs/15/release/VC_redist.x64.exe).

##### Anaconda2: `pip install` 

The installation steps for CNTK on Anaconda2 are identical to 

- [Anaconda3: First time CNTK installation](#first-time-cntk-installation)
- [Anaconda3: Upgrade an existing CNTK installation](#upgrade-an-existing-cntk-installation)

Just make sure you select Python 2.7 compatible wheel files from the URL table at the top of this page.

### 3. Install from Nightly Builds
If you prefer to install or upgrade CNTK from the latest nightly build rather than an official release, we offer CNTK nightly packages. You can access the CNTK packages from the latest nightly builds [here](https://cntk.ai/nightly-windows.html).  

If you are using a nightly build, you will need to separately install some third-party packages and add them to your PATH environment variable. Follow the below section for instructions. E.g. if you are installing the GPU version of CNTK, you will also need to install the GPU-specific packages listed in the following section.

#### Environment Variables and Required Packages

##### OPTIONAL: GPU-Specific Packages
If you intend to use CNTK with GPU, follow [this page](./Setup-GPU-Specific-Packages.md) to install and configure the environment accordingly.

After installing the aforementioned GPU packages, add them into your PATH environment variable, e.g.

```
setx PATH "C:\Program Files\NVIDIA Corporation\NVSMI;%PATH%"
setx PATH "C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.0\bin;%PATH%"
setx PATH "C:\local\cudnn-9.0-v7.0\cuda\bin;%PATH%"
```

##### MKL

The default CNTK math library is the [Intel Math Kernel Library (Intel MKL)](https://software.intel.com/en-us/intel-mkl/). Follow [this page](./Setup-MKL-on-Windows.md) to install it in your system.

* Prepend its path to environment variable `PATH`, e.g.:

```
    setx PATH "c:\local\mklml-2018.0.3\lib;%PATH%"
```

##### OPTIONAL: OpenCV

CNTK 2.2 requires [Open Source Computer Vision (OpenCV)](http://opencv.org/) to be installed, but it is optional for CNTK 2.3+. Follow [this page](./Setup-OpenCV-on-Windows.md) to install it.

You will need to install OpenCV for CNTK 2.3+ if you want to use the following components:

* CNTK Image Reader
* CNTK Image Writer - required to use TensorBoard's Image feature.

Prepend the environment variable `PATH` pointing to the OpenCV build folder, e.g.

```
setx PATH "C:\local\opencv3.10\build\x64\vc14\bin;%PATH%"
```


## Installing Samples and Tutorials

We provide various samples and tutorials with CNTK. After you've installed CNTK you can install the samples/tutorials and Jupyter notebooks. If you installed CNTK into a Python environment, make sure you activated the environment before running this command:
```
C:\> python -m cntk.sample_installer
```
This will download the samples/tutorials, install the required Python packages, and copy the samples into a directory named `CNTK-Samples-VERSION` (`VERSION` is replaced with the actual CNTK version) beneath your current working directory.

You can now follow the [standard description](./Setup-Test-Python.md) to test your installation from Python and run the tutorials or Jupyter notebooks.
