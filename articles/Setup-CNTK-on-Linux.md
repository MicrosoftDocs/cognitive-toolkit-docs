---
title:   Setup CNTK on Linux
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  03/12/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   cpp, python
---

# Setup CNTK on Linux

## CNTK Production Build and Test configuration

CNTK may be successfully run in many Linux configurations, but in case you want to avoid possible compatibility issues you may get yourself familiar with [CNTK Production Build and Test configuration](./Test-Configurations.md) where we list all dependency component and component versions that we use.

## CNTK as a Docker container

Before moving any further you may consider deploying CNTK as a Docker container. Read the [corresponding section](./CNTK-Docker-Containers.md).

## Current limitations and precautions

Please, read carefully this section before you proceed with your system configuration. The information below may save you a lot of time otherwise spent on build errors debugging.

> [!NOTE]
> This page assumes that you are trying to build CNTK's master branch.

### Expected component locations in configure and Makefile scripts

Today `configure` and `Makefile` scripts support only limited set of installation paths for all dependency components listed in this section. We know, that this is a limitation and will fix it soon (also if you feel like improving these scripts yourselves and [submit your proposed changes](./Contributing-to-CNTK.md) your help is welcome and much appreciated).

`configure` looks for all dependency components among the paths listed in `default_path_list` variable defined within the script. 

> [!IMPORTANT]
> If you want to modify `default_path_list` variable in `configure` to add a custom path for a certain dependency component be sure to check the correspondent section of `Makefile`. Otherwise you may get build errors due to inability of finding INCLUDE files, libraries, etc.

### Installation methods and paths of dependency components

Below we list all dependency components required to build CNTK and explain how to install them. We understand that there are many other ways to get the same components. However, if you prefer an alternative way of installation, please ensure that **you get the same thing**, because quite often alternative installation sources, namely network distribution  packages (like Debian, RPM, etc.) contain older versions of the software, miss some libraries, etc. In some sections below we specifically highlight these limitations, but please take it as a general precaution.

### Usage of `make -j`

In most of the sections we suggest using `make -j` command to invoke parallel build jobs and thus increasing the speed of the build process. However please be aware that on some systems and *especially on virtual machines* using `make -j` may result in "Out of memory" errors. If you face this, just use "plain" `make` or limit the number of jobs that run simultaneously (two simultaneous jobs usually work for the most of the systems - use the command `make -j 2`).  

### Simultaneous installation of different versions of the same development packages

Be very careful in case you would like to have several installations of some of the development packages mentioned below on the same system. It may result in very hard to debug build errors as you can see in [this post](https://github.com/Microsoft/CNTK/pull/93#issuecomment-183302591). 

And now let's proceed to the setup.

If you would like to know what prerequisite configuration is used in the CNTK production environment, i.e. what we use internally for building and testing, see [this section](./Test-Configurations.md)

## 64-bit OS

You need a 64-bit Linux installation to use CNTK.

## C++ Compiler

Ensure your installation has a C++ compiler. Many distributions do not include it by default. Refer to your platform documentation on how to check for and obtain a C++ compiler.

Example: for Ubuntu, run the following command:

```
dpkg --list | grep compiler
```

if in the output you do not see something like

```
g++-5 5.4.0-6ubuntu1~16.04.5 amd64 GNU C++ compiler
```

then a C++ compiler is not installed. If you have Ubuntu 1604, install gcc 5.4  with:

```
sudo apt-get install g++
```

## Git

Install Git on your system as described <a href="https://git-scm.com/download/linux" target="_blank">here</a>.

## MKL

The default CNTK math library is the [Intel Math Kernel Library (Intel MKL)](https://software.intel.com/en-us/intel-mkl/). Follow [this page](./Setup-MKL-on-Linux.md) to install it

## Open MPI

Install [Open Message Passing Interface Library (Open MPI)](http://www.open-mpi.org/).

We recommend installing from sources as described below because a lot of distribution packages contain older versions and miss the libraries required by CNTK. Current CNTK Open MPI version requirement is at least **1.10**. Please, check whether you have older version installations on your system and if you do, either uninstall them or ensure (via, e.g. symbolic links) that CNTK build procedure is using the required version. Otherwise you may get hard to debug build errors as you can see in [this post](https://github.com/Microsoft/CNTK/issues/269#issuecomment-202529551).

* Get the installation sources:

```
wget https://www.open-mpi.org/software/ompi/v1.10/downloads/openmpi-1.10.3.tar.gz
```

* Unpack, build and install Open MPI (to `/usr/local/mpi` in this example):

```
tar -xzvf ./openmpi-1.10.3.tar.gz
cd openmpi-1.10.3
./configure --prefix=/usr/local/mpi
make -j all
sudo make install
```

* Add the following environment variable to your current session and your `.bashrc` profile (Prepending the new path, ensures this version is used as opposed to a default version available through the OS):

```
export PATH=/usr/local/mpi/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/mpi/lib:$LD_LIBRARY_PATH
```

## Protobuf

We use Protocol Buffers for serialization. For installation please follow these steps:

* Install the required packages using

```
sudo apt-get install autoconf automake libtool curl make g++ unzip
```

* Download and unpack protobuf sources

```
wget https://github.com/google/protobuf/archive/v3.5.2.tar.gz
tar -xzf v3.5.2.tar.gz
```

* Compile protobuf

```
cd protobuf-3.5.2
./autogen.sh
./configure CFLAGS=-fPIC CXXFLAGS=-fPIC --disable-shared --prefix=/usr/local/protobuf-3.5.2
make -j $(nproc)
sudo make install
```

## ZLIB

zlib today is a part of many popular Linux distributions so with the high probability you have it preinstalled. However note, that libzip requires **zlib  version 1.1.2 or later** and this usually is NOT preinstalled. zlib must be installed before building Boost below.

See your platform documentation on how to get the required zlib package or get it directly from [zlib website](http://zlib.net/). 

Example: for Ubuntu use the following command:

```
sudo apt-get install zlib1g-dev
```

## LIBZIP

libzip is available in different network distribution packages, but we found that many of them contain old versions. Using these versions will likely result in build errors. So we strongly recommend to build libzip from sources as described below.

Note that the following procedure will install libzip to `/usr/local`. This is exactly where CNTK build procedure expects it (see [the beginning of this page](#expected-component-locations-in-configure-and-makefile-scripts) for details). If you want to install libzip to a different path see instructions in `INSTALL` file in the root of libzip distribution folder. **However beware that in this case you have to manually edit `configure` AND `Makefile` of CNTK to support this path**.

Use the following commands:

```
wget http://nih.at/libzip/libzip-1.1.2.tar.gz
tar -xzvf ./libzip-1.1.2.tar.gz
cd libzip-1.1.2
./configure
make -j all
sudo make install
```

Add the following environment variable to your current session and your `.bashrc` profile:

```
export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
```

## Boost Library

The [Boost Library](http://www.boost.org/) is a prerequisite for building the Microsoft Cognitive Toolkit. Follow these steps to install the Boost Library on your system:

```
sudo apt-get install libbz2-dev
sudo apt-get install python-dev

wget -q -O - https://sourceforge.net/projects/boost/files/boost/1.60.0/boost_1_60_0.tar.gz/download | tar -xzf - 
cd boost_1_60_0 
./bootstrap.sh --prefix=/usr/local/boost-1.60.0
sudo ./b2 -d0 -j"$(nproc)" install  
```

----------
## GPU Specific Packages
If you intend to use CNTK with GPU support, follow [this page](./Setup-GPU-Specific-Packages-Linux.md) to install and configure the environment accordingly.

## OPTIONAL. SWIG.

If you want to take advantage of CNTK from Python, you will need to install [SWIG](http://swig.org).
SWIG is also a requirement to build the CNTK Evaluation libraries for Java.
To install it, run the script: `[CNTK clone root]/Tools/devInstall/Linux/install-swig.sh`.
This creates the installed version in the folder `/usr/local/swig-3.0.10`.

## OPTIONAL. CNTK v2 Python support

This section describes how to build CNTK v2 with Python support.

**Step 1**: Build Python APIs

* Install the [SWIG tool](#optional-swig) if you have not done so yet.
* Install [Anaconda3 4.1.1 (64-bit)](https://repo.continuum.io/archive/Anaconda3-4.1.1-Linux-x86_64.sh)
* If you already have a CNTK Python environment (called `cntk-py36`, `cntk-py35`, or `cntk-py27`) you can update it with the latest required packages with the following commands:

```
# For cntk-py36:
conda env update --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py36-environment.yml --name cntk-py36
# For cntk-py35:
conda env update --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py35-environment.yml --name cntk-py35
# For cntk-py27:
conda env update --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py27-environment.yml --name cntk-py27
```

* If you do not have a CNTK Python environment yet, you may choose between a Python 2.7, 3.5 or 3.6 based CNTK Python environment.
* Create your Python environment of choice in your existing Python 3.5 Anaconda or Miniconda installation using *one* of the following commands, depending on your desired Python version:

```
# For a Python 3.6 based version:
conda env create --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py36-environment.yml
# For a Python 3.5 based version:
conda env create --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py35-environment.yml
# For a Python 2.7 based version:
conda env create --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py27-environment.yml
```

Note: Make sure that the Python environment updated or created above is activated for the remainder of the instructions.
For example, if you have Python 3.5 based environment called `cntk-py35` run this command:

```
source activate cntk-py35
```

Similarly, for a Python 3.6, or 2.7 based environment.

**Step 2**: Uninstall previous CNTK package

* If you previously installed any version of the CNTK pip-package on your machine, uninstall it by running: `pip uninstall cntk`

**Step 3**: Build Python Package

* To configure a build with Python, include this two option when running `configure`:

```
--with-swig[=directory]
```

and *one* of the following (whatever applies to your environment):

```
--with-py36-path[=directory]
--with-py35-path[=directory]
--with-py27-path[=directory]
```

* Only Release builds are supported at this stage. For example, if you installed SWIG to `/usr/local/swig-3.0.10` and your Python environment is located at `$HOME/anaconda3/envs/cntk-py35` provide these additional parameters to `configure`:

```
--with-swig=/usr/local/swig-3.0.10 --with-py35-path=$HOME/anaconda3/envs/cntk-py35
```

* Afterwards, run make as you normally would, which will build the CNTK Python module inside `bindings/python/cntk` and also produce a package (.whl) in a subfolder python of your build output folder (e.g., `build/gpu/release/python`).

```
cd [CNTK clone root]
export PYTHONPATH=$PWD/bindings/python:$PYTHONPATH
cd [CNTK clone root]/bindings/python
export LD_LIBRARY_PATH=$PWD/cntk/libs:$LD_LIBRARY_PATH
```

> [!NOTE]
> In contrast to the setup shown for the Pip package installation, here we will load the CNTK module from the CNTK repository clone, not as an installed package in your Python environment. (Hence also the difference in setting up `PYTHONPATH`)

**Step 4**: Verify setup

Python

* Run the Python examples from inside the `[CNTK clone root]/Tutorials` or `[CNTK clone root]/Examples` directories, to verify your  installation. For example, go to the folder `[CNTK clone root]/Tutorials/NumpyInterop` and run `python FeedForwardNet.py`. You should see the following output on the console:

```
Minibatch: 0, Train Loss: 0.7915553283691407, Train Evaluation Criterion: 0.48

Minibatch: 20, Train Loss: 0.6266774368286133, Train Evaluation Criterion: 0.48

Minibatch: 40, Train Loss: 1.0378565979003906, Train Evaluation Criterion: 0.64

Minibatch: 60, Train Loss: 0.6558118438720704, Train Evaluation Criterion: 0.56
```

----------

## OPTIONAL. OpenCV

CNTK 2.2 requires [Open Source Computer Vision (OpenCV)](http://opencv.org/) to be installed but it is optional after CNTK 2.3. Follow [this page](./Setup-OpenCV-on-Linux.md) to install it

## OPTIONAL. Java

To build the Java bindings for the CNTK Evaluation library, install the [SWIG tool](#optional-swig) if you have not done so yet. Also, a Java Development Kit (JDK) is required. Currently we use 64-bit [OpenJDK 8](http://openjdk.java.net/install/).

The configure script provides `--with-jdk` option to specify the JDK directory manually, if it cannot be found by default.

## Getting CNTK Source code

Before proceeding further, please note, that if you plan on making modifications to the CNTK code you should read the information on [Developing and Testing](./Developing-and-Testing.md).

Use [Git](#git) to clone the CNTK Repository and access the source code:

```
git clone https://github.com/Microsoft/cntk
cd cntk
git submodule update --init external/gsl
git submodule update --init Source/CNTKv2LibraryDll/proto/onnx/onnx_repo
git submodule update --init -- Source/Multiverso
```

Submodule Multiverso is used for enable [DataParallelASGD](./Multiple-GPUs-and-machines.md#8-data-parallel-training-with-parameter-server) for training.

**Optional** If you don't need DataParallelASGD support, then pass the option `--asgd=no` to the configure command.

## Building CNTK

To build CNTK use the following commands (we assume that the CNTK repository was cloned to `~/Repos/cntk`):

```
cd ~/Repos/cntk
mkdir build/release -p
cd build/release
../../configure
```

Ensure that the `configure` output corresponds to the packages you installed in the previous sections. I.e. ensure that `configure` finds CUDA if installed, etc.

Do the following to build CNTK using all cores to minimize build time.  Note that on some computer, this can overwhelm your system leading to hangs or breaks during the build.

```
make -j all
```

If the above overwhelms your computer, try specifying fewer cores.  For example if you have more than 2 cores, and would like to keep 2 cores free from the build, you can try:

```
make -j"$(($(nproc)  - 2))" all
```

If you want to be absolutely safe, just use 1 core:
```
make all
```

This should produce a release build of CNTK. In case you would like to get a debug build use the following parameter when invoking `configure`:

```
../../configure --with-buildtype=debug
```

## Quick test of CNTK build functionality

To ensure that CNTK is working properly in your system, you can quickly run an example from the
[Hello World - Logistic Regression](https://github.com/Microsoft/CNTK/tree/release/latest/Tutorials/HelloWorld-LogisticRegression) tutorial.
This example trains a simple network and can be directed to use either CPU or GPU, which helps quickly ensure that CNTK is
functioning properly.

Below we assume that the CNTK repository is cloned to `~/Repos/cntk` and `build/release` was used as a sub-directory for the build.

* Provide the path to the CNTK binaries and change to the `Tutorials/HelloWorld-LogisticRegression` directory:

```
export PATH=$HOME/Repos/cntk/build/release/bin:$PATH
cd ~/Repos/cntk/Tutorials/HelloWorld-LogisticRegression
```

First try the example:

```
cntk configFile=lr_bs.cntk makeMode=false
``` 

If the sample runs, i.e., if there are no error messages, you will get output related first to reading the configuration, followed
by the output of the actual network training.

### Trying CNTK with GPU

If you built CNTK for GPU usage, try using the GPU by executing the following commands:

```
cntk configFile=lr_bs.cntk makeMode=false deviceId=auto
```

Near the beginning of the output you should see a line confirming a GPU was used:

```
Model has 9 nodes. Using GPU 0.
```

Note that GPU ID may be different. The `deviceId` parameter defines what processor to use for computation.

* `deviceId=-1` means use CPU. Default value
* `deviceId=X` where X is an integer >=0 means use GPU X, i.e. `deviceId=0` means GPU 0, etc.
* `deviceId=auto` means use GPU, select GPU automatically

## Contributing to CNTK code

If you plan modifications to the code you should read the information on [Developing and Testing](./Developing-and-Testing.md).

**Next steps**
* [Developing and Testing](./Developing-and-Testing.md)
* [CNTK usage from Brainscript](./CNTK-usage-overview.md)
* [Examples](./Examples.md)
