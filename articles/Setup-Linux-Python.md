---
title:   Setup Linux Python
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/03/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Setup Linux Python

## Installing CNTK for Python on Linux (Ubuntu 14.04 or Ubuntu 16.04)

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK) to use from Python in Linux (Ubuntu 14.04 or Ubuntu 16.04).
If you are looking for any other kind of support to setup a CNTK build environment or installing CNTK on your system, you should go [here](./Setup-CNTK-on-your-machine.md) instead. 

Depending on the Python and CNTK version (CPU-Only, GPU, GPU-1bit-SGD) we supply different wheel (.whl) files to install CNTK.
Please select the correct installation from the list below, and substitute the name and/or link during the installation:
Note that the CNTK GPU-1bit-SGD version is licensed under a specific [1bit-SGD License](./CNTK-1bit-SGD-License.md) which is MORE restrictive, than the major CNTK License.

* Easy pip install for [Anaconda3 4.1.1](#anaconda3)
* Easy pip install for [Anaconda2 4.3.0](#anaconda2)

|Python | Flavor | URL
|:------|:-------|:--------|
| 2.7 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp27-cp27mu-linux_x86_64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp27-cp27mu-linux_x86_64.whl` |
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp27-cp27mu-linux_x86_64.whl` |
| 3.4 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp34-cp34m-linux_x86_64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp34-cp34m-linux_x86_64.whl` |
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp34-cp34m-linux_x86_64.whl` |
| 3.5 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp35-cp35m-linux_x86_64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp35-cp35m-linux_x86_64.whl` |
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp35-cp35m-linux_x86_64.whl` |
| 3.6 | CPU-Only | `https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp36-cp36m-linux_x86_64.whl`
|  | GPU | `https://cntk.ai/PythonWheel/GPU/cntk-2.0-cp36-cp36m-linux_x86_64.whl` |
|  | GPU-1bit-SGD | `https://cntk.ai/PythonWheel/GPU-1bit-SGD/cntk-2.0-cp36-cp36m-linux_x86_64.whl` |

### Prerequisites

CNTK requires OpenMPI 1.10.x to be installed on your system.
On Ubuntu 16.04 install it like this:
```
sudo apt-get install openmpi-bin
```
On Ubuntu 14.04. please follow the instructions [here](./Setup-CNTK-on-Linux.md#open-mpi) to build it yourself.
Make sure that it's libraries can be found, e.g., by setting up `LD_LIBRARY_PATH`.

### Anaconda3

We have been testing CNTK with Anaconda3 4.1.1 and Python versions 2.7, 3.4, 3.5, as well as Anaconda3 4.3.1 with Python version 3.6. If you do not have an Anaconda3 Python installation, install [Anaconda3 4.1.1 Python for Linux (64-bit)](https://repo.continuum.io/archive/Anaconda3-4.1.1-Linux-x86_64.sh).

Below we assume that the [prerequisites above](#prerequisites) are satisfied.
If you plan on using a GPU enabled version of CNTK, you will need a CUDA 8 compliant graphics card and up-to-date graphics drivers installed on your system.
Also, we assume Anaconda is installed and that it is listed before any other Python installations in your PATH.

#### pip install without an environment

This is the easiest option and the only reason to avoid it is if you require specific versions of certain packages. If you have other packages that require an old version of numpy skip to [this section](#pip-install-in-an-environment).

##### First time CNTK installation

If this is the first time you install CNTK then run
```
$ pip install <url>
```
where `<url>` is the corresponding wheel file URL in the table on the top of this page. E.g., if you have Python 3.5 run
```
$ pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp35-cp35m-linux_x86_64.whl
```

Continue with a [quick installation test](#quick-installation-test)

##### Upgrade an existing CNTK installation

If you already have a previous version of CNTK installed, you can install a new version of CNTK over your existing installation. It is important to supply the `--upgrade` and `--no-deps` options.
```
$ pip install --upgrade --no-deps <url>
```
where `<url>` is the corresponding wheel file URL in the table on the top of this page. Once you have completed this upgrade step, you can start working with CNTK in Python or [install samples and tutorials](#installing-samples-and-tutorials).

##### Quick installation test

A quick test that the installation succeeded can be done by querying the CNTK version:
```
$ python -c "import cntk; print(cntk.__version__)"
```

You now have successfully installed CNTK, you can start developing / training / evaluating with CNTK in Python!

Continue with [installing samples and tutorials](#installing-samples-and-tutorials)

#### pip install in an environment

Below we will create a new Python 3.4 environment inside Anaconda called `cntk-py34` and will pip-install CNTK into this environment. If you want a different Python version or environment name, please adjust the parameters accordingly.

Open a command shell, create the environment, make it active, and pip-install CNTK:
```
$ conda create --name cntk-py34 python=3.4 numpy scipy h5py jupyter
$ activate cntk-py34
$ pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0-cp34-cp34m-linux_x86_64.whl
```
A quick test that the installation succeeded can be done by querying the CNTK version:
```
$ python -c "import cntk; print(cntk.__version__)"
```

You now have successfully installed CNTK, you can start developing / training / evaluating with CNTK in Python!

Continue with a [installing samples and tutorials](#installing-samples-and-tutorials)

### Anaconda2

If you require a Python 2.7 root environment, we recommend you install [Anaconda2 4.3.0 Python for Linux (64-bit)](https://repo.continuum.io/archive/Anaconda2-4.3.0-Linux-x86_64.sh).
Below we assume that the [prerequisites above](#prerequisites) are satisfied.
If you plan on using a GPU enabled version of CNTK, you will need a CUDA 8 compliant graphics card and up-to-date graphics drivers installed on your system.
Also, we assume Anaconda2 is installed and that it is listed before any other Python installations in your PATH.

#### Anaconda2: pip install

The installation steps for CNTK on Anaconda2 are identical to

- [Anaconda3 First time CNTK installation](#first-time-cntk-installation)
- [Anaconda3: Upgrade an existing CNTK installation](#upgrade-an-existing-cntk-installation)

Just make sure you select Python 2.7 compatible wheel files from the URL table at the top of this page.

#### Installing Samples and Tutorials

We provide various samples and tutorials with CNTK. After you installed CNTK you can install the samples/tutorials and Jupyter notebooks. If you installed CNTK into a Python environment, make sure you activated the environment before running this command:
```
$ python -m cntk.sample_installer
```
This will download the samples/tutorials, install the required Python packages, and copy the samples into a directory named `CNTK-Samples-VERSION` (`VERSION` is replaced with the actual CNTK version) beneath your current working directory.

You can now follow the [standard description](./Setup-Test-Python.md) to test your installation from Python and run the tutorials or Jupyter notebooks.
