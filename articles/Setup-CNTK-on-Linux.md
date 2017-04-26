### CNTK Production Build and Test configuration
CNTK may be successfully run in many Linux configurations, but in case you want to avoid possible compatibility issues you may get yourself familiar with [CNTK Production Build and Test configuration](./Test-Configurations) where we list all dependency component and component versions that we use.

### CNTK as a Docker container
Before moving any further you may consider deploying CNTK as a Docker container. Read the [corresponding section](./CNTK-Docker-Containers).

### Current limitations and precautions
Please, read carefully this section before you proceed with your system configuration. The information below may save you a lot of time otherwise spent on build errors debugging.

#### Expected component locations in `configure` and `Makefile` scripts
Today `configure` and `Makefile` scripts support only limited set of installation paths for all dependency components listed in this section. We know, that this is a limitation and will fix it soon (also if you feel like improving these scripts yourselves and [submit your proposed changes](.//Contributing-to-CNTK) your help is welcome and much appreciated).

`configure` looks for all dependency components among the paths listed in `default_path_list` variable defined within the script. 

**IMPORTANT!** If you want to modify `default_path_list` variable in `configure` to add a custom path for a certain dependency component be sure to check the correspondent section of `Makefile`. Otherwise you may get build errors due to inability of finding INCLUDE files, libraries, etc.

#### Installation methods and paths of dependency components
Below we list all dependency components required to build CNTK and explain how to install them. We understand that there are many other ways to get the same components. However, if you prefer an alternative way of installation, please ensure that **you get the same thing**, because quite often alternative installation sources, namely network distibution  packagespackages (like Debian, RPM, etc.) contain older versions of the software, miss some libraries, etc. In some sections below we specifically highlight these limitations, but please take it as a general precaution.

#### Using of `make -j`
In most of the sections we suggest using `make -j` command to invoke parallel build jobs and thus increasing the speed of the build process. However please be aware that on some systems and *especially on virtual machines* using `make -j` may result in "Out of memory" errors. If you face this, just use "plain" `make` or limit the number of jobs that run simultaneously (two simultaneous jobs usually work for the most of the systems - use the command `make -j 2`).  

#### Simultaneous installation of different versions of the same development packages
Be very careful in case you would like to have several installations of some of the development packages mentioned below on the same system. It may result in very hard to debug build errors as you can see in [this post](../pull/93#issuecomment-183302591). 

And now let's proceed to the setup.

If you would like to know what prerequisite configuration is used in the CNTK production environment, i.e. what we use internally for building and testing, see [this section](./Test-Configurations)

### 64-bit OS
You need a 64-bit Linux installation to use CNTK.

### C++ Compiler
Ensure your installation has a C++ compiler. Many distributions do not include it by default. Refer to your platform documentation on how to check for and obtain a C++ compiler.

Example: for Ubuntu, run the following command:
```
dpkg --list | grep compiler
```
if in the output you do not see something like
```
g++-4.8 4.8.4-2ubuntu1~14.04 amd64 GNU C++ compiler
```
then a C++ compiler is not installed. If you have Ubuntu 1404, install gcc 4.8  with:
```
sudo apt-get install g++
```

If you have Ubuntu 1604 or something else, install gcc 4.8 by following: [http://askubuntu.com/questions/26498/choose-gcc-and-g-version](/http://askubuntu.com/questions/26498/choose-gcc-and-g-version.md) 

### Git

Install Git on your system as described <a href="https://git-scm.com/download/linux" target="_blank">here</a>.

###  MKL

The default CNTK math library is the [Intel Math Kernel Library (Intel MKL)](https://software.intel.com/en-us/intel-mkl/).
CNTK supports using the Intel MKL via a custom library version ("CNTK custom MKL").

**IMPORTANT**
You can NOT directly build CNTK using a regular installed Intel MKL SDK, the build is configured to work with a custom generated CNTK custom MKL library (This way you don't need to go through the process of installing the complete Intel MKL SDK). You can obtain the CNTK custom MKL library if you follow the steps below. If you need to build your own custom version, you can find the needed information [here](https://github.com/Microsoft/CNTK/tree/master/Dependencies/CNTKCustomMKL).

Installing the CNTK custom MKL library: 

* Create a directory on your machine to hold CNTK custom MKL versions, e.g.:
```
sudo mkdir /usr/local/CNTKCustomMKL
```
* Download the required CNTK custom MKL from the [CNTK web site](https://www.cntk.ai/mkl).
  Un-tar it into your CNTK MKL path, creating a numbered sub directory within.
  For example, if you are on latest master, download `CNTKCustomMKL-Linux-3.tgz` and extract its contents to `/usr/local/CNTKCustomMKL/3`:
```
wget https://www.cntk.ai/mkl/CNTKCustomMKL-Linux-3.tgz
sudo tar -xzf CNTKCustomMKL-Linux-3.tgz -C /usr/local/CNTKCustomMKL
```
  Note: if you want to build other CNTK source versions, you may need to install alternate CNTK custom MKL versions.
  Check the variable `cntk_custom_mkl_version` in CNTK's configuration script `configure` to determine which.

* When configuring the build (cf. below), specify the option `--with-mkl` or `--with-mkl=<directory>`, e.g., `--with-mkl=/usr/local/CNTKCustomMKL`.

### Open MPI

Install [Open Message Passing Interface Library (Open MPI)](http://www.open-mpi.org/).

We recommend installing from sources as described below because a lot of distribution packages contain older versions and miss the libraries required by CNTK. Current CNTK Open MPI version requirement is at least **1.10**. Please, check whether you have older version installations on your system and if you do, either deinstall them or ensure (via, e.g. symbolic links) that CNTK build procedure is using the required version. Otherwise you may get hard to debug build errors as you can see in [this post](../issues/269#issuecomment-202529551).
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
* Add the following environment variable to your current session and your `.bashrc` profile
(Prepending the new path, ensures this version is used as opposed to a default version available through the OS):
```
export PATH=/usr/local/mpi/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/mpi/lib:$LD_LIBRARY_PATH
```

### Protobuf

We use Protocol Buffers for serialization. For installation please follow these steps:

1) install the required packages using
```
sudo apt-get install autoconf automake libtool curl make g++ unzip
```

2) Download and unpack protobuf sources
```
wget https://github.com/google/protobuf/archive/v3.1.0.tar.gz
tar -xzf v3.1.0.tar.gz
```
3) Compile protobuf
```
cd protobuf-3.1.0
./autogen.sh
./configure CFLAGS=-fPIC CXXFLAGS=-fPIC --disable-shared --prefix=/usr/local/protobuf-3.1.0
make -j $(nproc)
sudo make install
```

### ZLIB
zlib today is a part of many popular Linux distributions so with the high probability you have it preinstalled. However note, that libzip requires **zlib  version 1.1.2 or later** and this usually is NOT preinstalled. zlib must be installed before building Boost below.

 See your platform documentation on how to get the required zlib package or get it directly from [zlib website](http://zlib.net/). 

Example: for Ubuntu use the following command:
```
sudo apt-get install zlib1g-dev
```

### LIBZIP
libzip is available in different network distribution packages, but we found that many of them contain old versions. Using these versions will likely result in build errors. So we strongly recommend to build libzip from sources as described below.

Note that the following procedure will install libzip to `/usr/local`. This is exactly where CNTK build procedure expects it (see [the beginning of this page](./Setup-CNTK-on-Linux#expected-component-locations-in-configure-and-makefile-scripts) for details). If you want to install libzip to a different path see instructions in `INSTALL` file in the root of libzip distribution folder. **However beware that in this case you have to manually edit `configure` AND `Makefile` of CNTK to support this path**.

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

### Boost Library

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
### GPU Specific Packages
This section outlines the packages you need to setup in order for CNTK to leverage GPUs.

If you do NOT intend to use any GPUs with CNTK and will rely on the CPU only, you may skip directly to the [next general section](./Setup-CNTK-on-Linux#optional-boost-library). Otherwise proceed further.

#### Checking your GPU compatibility and getting the latest driver
You need a [CUDA](https://developer.nvidia.com/cuda-zone)-compatible graphic card available to use CNTK GPU capabilities. You can check whether your card is CUDA-compatible [here](https://developer.nvidia.com/cuda-gpus) and [here (for older cards)](https://developer.nvidia.com/cuda-legacy-gpus). Your GPU card [Compute Capability (CC)](http://docs.nvidia.com/cuda/cuda-c-programming-guide/index.html#compute-capability) must be 3.0 or more.

If you have the required card, install the latest driver:
* Select your card and download the driver pack from [here](http://www.nvidia.com/Download/index.aspx?lang=en-us) (usually available as `.run` file)
* If your X Windows manager is running, the driver installation will likely fail. Open a remote terminal session to your machine and stop the X Windows manager. Refer to your platform documentation for the exact commands.

Example: for Ubuntu, use the following command (assuming `lightdm` is your X Windows manager):
```
sudo stop lightdm
```

* Install the driver as in the example below (note that the file name may be different for your system):
```
sudo chmod +x ./NVIDIA-Linux-x86_64-367.55.run
sudo ./NVIDIA-Linux-x86_64-367.55.run
```
We recommend accepting the default installation options

Note that the driver installation program may complain about nouveau kernel driver. Refer to your platform documentation for instructions for disabling it. For Ubuntu you may use [this set of instructions](http://askubuntu.com/a/451248).

* If stopped during the steps above, start X Windows manager. Refer to your platform documentation for the exact commands.

Example: for Ubuntu, use the following command (in case of `lightdm` as your X Windows manager):
```
sudo start lightdm
```

#### CUDA 8.0

The procedure below explains how to install CUDA using `.run` file distribution. You can also use `DEB` or `RPM` packages installation. You will find the package for your system at [NVIDIA CUDA 8.0 Download page](https://developer.nvidia.com/cuda-downloads) and installation instructions in [CUDA Online Documentation](http://docs.nvidia.com/cuda/pdf/CUDA_Installation_Guide_Linux.pdf).

Download and install the NVIDIA CUDA 8.0 Toolkit:
* Find the `.run` file for your platform [here](https://developer.nvidia.com/cuda-downloads) and download it.

* If your X Windows manager is running, the installation will likely fail. Open a remote terminal session to your machine and stop the X Windows manager. Refer to your platform documentation for the exact commands.

Example: for Ubuntu use the following command (in case of `lightdm` as your X Windows manager):
```
sudo stop lightdm
```
* Install the CUDA 8.0 Toolkit (note that the `.run` file name may be different for your system):
```
chmod +x ./cuda_8.0.44_linux.run
sudo ./cuda_8.0.44_linux.run
```
**IMPORTANT!** When prompted by the installer:
```
Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 367.48?
(y)es/(n)o/(q)uit:
```
select `no` if you have already installed the latest driver by performing the steps in the [previous section](./Setup-CNTK-on-Linu#checking-your-gpu-compatibility-and-getting-the-latest-driver). If you have not done it, select `yes`, but we **strongly recommend** updating to the latest driver after installing CUDA toolkit.

If you declined the driver installation from the CUDA 8.0 package as described in the previous section, you will get the following warning at the end of the installation:
```
***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 361.00 is required for CUDA 8.0 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run -silent -driver
```
Ignore it

* If stopped during the steps above, start X Windows manager. Refer to your platform documentation for exact commands.

Example: for Ubuntu use the following command (in case of `lightdm` as your X Windows manager):
```
sudo start lightdm
```
* Add the following environment variable to your current session and your `.bashrc` profile (if you modified the default paths during the CUDA installation, change the values below accordingly):
```
export PATH=/usr/local/cuda-8.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-8.0/lib64:$LD_LIBRARY_PATH
```
The next step is optional. You may skip further to the [next section](./Setup-CNTK-on-Linu#gpu-deployment-kit).

##### OPTIONAL. Verifying CUDA 8.0 installation

You may verify your CUDA installation by compiling the CUDA samples (we assume the default paths were used during the CUDA installation). Note that building all samples is a lengthy operation:
```
cd ~/NVIDIA_CUDA-8.0_Samples/
make
```

After the successful build invoke `deviceQuery` utility:
```
~/NVIDIA_CUDA-8.0_Samples/1_Utilities/deviceQuery/deviceQuery
```

If everything works well, you should get an output similar to the one below:
```
./deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "GeForce GTX 960"
  CUDA Driver Version / Runtime Version          8.0 / 8.0
  CUDA Capability Major/Minor version number:    5.2
  Total amount of global memory:                 2025 MBytes (2123235328 bytes)
  ( 8) Multiprocessors, (128) CUDA Cores/MP:     1024 CUDA Cores
  GPU Max Clock rate:                            1253 MHz (1.25 GHz)
  Memory Clock rate:                             3505 Mhz
  Memory Bus Width:                              128-bit
  L2 Cache Size:                                 1048576 bytes
  Maximum Texture Dimension Size (x,y,z)         1D=(65536), 2D=(65536, 65536), 3D=(4096, 4096, 4096)
  Maximum Layered 1D Texture Size, (num) layers  1D=(16384), 2048 layers
  Maximum Layered 2D Texture Size, (num) layers  2D=(16384, 16384), 2048 layers
  Total amount of constant memory:               65536 bytes
  Total amount of shared memory per block:       49152 bytes
  Total number of registers available per block: 65536
  Warp size:                                     32
  Maximum number of threads per multiprocessor:  2048
  Maximum number of threads per block:           1024
  Max dimension size of a thread block (x,y,z): (1024, 1024, 64)
  Max dimension size of a grid size    (x,y,z): (2147483647, 65535, 65535)
  Maximum memory pitch:                          2147483647 bytes
  Texture alignment:                             512 bytes
  Concurrent copy and kernel execution:          Yes with 2 copy engine(s)
  Run time limit on kernels:                     No
  Integrated GPU sharing Host Memory:            No
  Support host page-locked memory mapping:       Yes
  Alignment requirement for Surfaces:            Yes
  Device has ECC support:                        Disabled
  Device supports Unified Addressing (UVA):      Yes
  Device PCI Domain ID / Bus ID / location ID:   0 / 3 / 0
  Compute Mode:
     < Default (multiple host threads can use ::cudaSetDevice() with device simultaneously) >

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 8.0, CUDA Runtime Version = 8.0, NumDevs = 1, Device0 = GeForce GTX 960
Result = PASS
```
#### GPU Deployment Kit
Starting from CUDA version 8, the GPU Deployment Kit is a part of the CUDA package and is no longer required to be installed separately.

#### CUB

Get and install NVIDIA CUB using the commands below.
**IMPORTANT!** Install NVIDIA CUB using the exact version and target path as specified below. This is necessary because it is expected by the CNTK build configuration program.

Use the following commands:
```
wget https://github.com/NVlabs/cub/archive/1.4.1.zip
unzip ./1.4.1.zip
sudo cp -r cub-1.4.1 /usr/local
```

#### cuDNN

Install [NVIDIA CUDA Deep Neural Network library (cuDNN)](https://developer.nvidia.com/cuDNN).

**IMPORTANT!** If you previously installed cuDNN for an older version make sure that you upgrade to the CUDA 8.0 compatible version

**IMPORTANT!** Install cuDNN using the exact version and target path as specified below. This is necessary because it is expected by the CNTK build configuration program.

* Use the following commands:
```
wget http://developer.download.nvidia.com/compute/redist/cudnn/v5.1/cudnn-8.0-linux-x64-v5.1.tgz
tar -xzvf ./cudnn-8.0-linux-x64-v5.1.tgz
sudo mkdir /usr/local/cudnn-5.1
sudo cp -r cuda /usr/local/cudnn-5.1
```

* Add the following environment variable to your current session and your `.bashrc` profile:
```
export LD_LIBRARY_PATH=/usr/local/cudnn-5.1/cuda/lib64:$LD_LIBRARY_PATH
```
----------

### OPTIONAL. NCCL.

[NVIDIA's NCCL library](https://github.com/NVIDIA/nccl) provides optimized primitives for collective
multi-GPU communication on Linux.
CNTK can take advantage of these accelerated primitives for parallel jobs running on a single host
(cf. [here](./Multiple-GPUs-and-machines) for an introduction into parallel training with CNTK).

Please follow build instructions [here](https://github.com/NVIDIA/nccl) to build the NVIDIA NCCL library.
Then, use the CNTK configure option `--with-nccl=<path>` to enable building with NVIDIA NCCL.
For example, if NCCL builds outputs are in folder `$HOME/nccl/build`, use
`configure --with-nccl=$HOME/nccl/build` (plus additional options) to build with NVIDIA NCCL support.

> Note: Currently, CNTK's support for NVIDIA NCCL is limited to data-parallel SGD with 32/64 gradient bits, using
the CNTK binary. Support for additional parallelization methods and CNTK v2 will be added in the future.


This completes GPU Specific section


----------
### OPTIONAL. CNTK v2 Python support.

This section describes how to build CNTK v2 with Python support.

**Step 1**: Build Python APIs
- Install SWIG by running the script: `[CNTK clone root]/bindings/python/swig_install.sh`. This creates the installed version in a `$HOME/swig-3.0.10/root` folder

- Install [Anaconda3 4.1.1 (64-bit)](https://repo.continuum.io/archive/Anaconda3-4.1.1-Linux-x86_64.sh)

- If you already have a CNTK Python 3.5 environment (called `cntk-py35`) you can update it with the latest required packages with the following commands:
```
conda env update --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py35-environment.yml --name cntk-py35
```

- If you already have a CNTK Python environment (called `cntk-py35`, `cntk-py34`, or `cntk-py27`) you can update it with the latest required packages with the following commands:
```
# For cntk-py35:
conda env update --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py35-environment.yml --name cntk-py35
# For cntk-py34:
conda env update --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py34-environment.yml --name cntk-py34
# For cntk-py27:
conda env update --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py27-environment.yml --name cntk-py27
```

- If you do not have a CNTK Python environment yet, you may choose between a Python 2.7, 3.4, or 3.5 based CNTK Python environment.

- Create your Python environment of choice in your existing Python 3.5 Anaconda or Miniconda installation using *one* of the following commands, depending on your desired Python version:
```
# For a Python 3.5 based version:
conda env create --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py35-environment.yml
# For a Python 3.4 based version:
conda env create --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py34-environment.yml
# For a Python 2.7 based version:
conda env create --file [CNTK clone root]/Scripts/install/linux/conda-linux-cntk-py27-environment.yml
```
Note: Make sure that the Python environment updated or created above is activated for the remainder of the instructions.
For example, if you have Python 3.5 based environment called `cntk-py35` run this command:
```
source activate cntk-py35
```
Similarly, for a Python 3.4 or 2.7 based environment.

**Step 2**: Uninstall previous CNTK 2.0 package
- If you previously installed any version of the CNTK 2.0 pip-package on your machine, uninstall it by running: `pip uninstall cntk`

**Step 3**: Build Python Package

- To configure a build with Python, include this two option when running `configure`:
```
--with-swig[=directory]
```
and *one* of the following (whatever applies to your environment):
```
--with-py35-path[=directory]
--with-py34-path[=directory]
--with-py27-path[=directory]
```

- Only Release builds are supported at this stage. For example, if you installed SWIG to `$HOME/swig-3.0.10` and your Python environment is located at `$HOME/anaconda3/envs/cntk-py35` provide these additional parameters to configure:
```
--with-swig=$HOME/swig-3.0.10/root --with-py35-path=$HOME/anaconda3/envs/cntk-py35
```

- Afterwards, run make as you normally would, which will build the CNTK Python module inside `bindings/python/cntk` and also produce a package (.whl) in a subfolder python of your build output folder (e.g., `build/gpu/release/python`).
```
cd [CNTK clone root]
export PYTHONPATH=$PWD/bindings/python:$PYTHONPATH
cd [CNTK clone root]/bindings/python
export LD_LIBRARY_PATH=$PWD/cntk/libs:$LD_LIBRARY_PATH
```

> Note: in contrast to the setup shown for the Pip package installation, here we will load the CNTK module from the CNTK repository clone, not as an installed package in your Python environment.
> (Hence also the difference in setting up `PYTHONPATH`)

**Step 4**: Verify setup

Python

- Run the Python examples from inside the `[CNTK clone root]/Tutorials` or `[CNTK clone root]/Examples` directories, to verify your
  installation. For example, go to the folder `[CNTK clone root]/Tutorials/NumpyInterop` and run `python
  FeedForwardNet.py`. You should see the following output on the console:
```
Minibatch: 0, Train Loss: 0.7915553283691407, Train Evaluation Criterion: 0.48

Minibatch: 20, Train Loss: 0.6266774368286133, Train Evaluation Criterion: 0.48

Minibatch: 40, Train Loss: 1.0378565979003906, Train Evaluation Criterion: 0.64

Minibatch: 60, Train Loss: 0.6558118438720704, Train Evaluation Criterion: 0.56
```

### OPTIONAL. OpenCV

If you want to build the **CNTK Image Reader**, you need to install [Open Source Computer Vision (OpenCV)](http://opencv.org/).

Some aspects to consider:
* OpenCV requires at least 5.5 GB of free disk space
* Building OpenCV is a lengthy process and can take a couple of hours or even more
* If you plan to use both CUDA and OpenCV, [install CUDA first](./Setup-CNTK-on-Linux#cuda-75)

OpenCV can have many interfaces and options.
In this section we cover only parts necessary to build CNTK.
Read more about installing OpenCV [here](http://docs.opencv.org/3.1.0/d7/d9f/tutorial_linux_install.html) and more generally [here](http://docs.opencv.org/3.1.0/df/d65/tutorial_table_of_content_introduction.html).

**IMPORTANT!** Install OpenCV using the exact version and target path as specified below. This is necessary because it is expected by the CNTK build configuration program.

* Install OpenCV prerequisites. See [OpenCV installation instructions](http://docs.opencv.org/3.1.0/d7/d9f/tutorial_linux_install.html) for the full package list and explanations. See your platform documentation on how to install the packages. 

Example: for Ubuntu use the following command:
```
sudo apt-get install cmake libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
```
* Get OpenCV and install it. Use the following commands:
```
wget https://github.com/Itseez/opencv/archive/3.1.0.zip
unzip 3.1.0.zip
cd opencv-3.1.0
mkdir release
cd release
cmake -D WITH_CUDA=OFF -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local/opencv-3.1.0 ..
make all
sudo make install
```
Note that in the instructions above we suggest using "plain" `make` rather than `make -j`. We found that using `make -j` with OpenCV results in unstable system behavior and may result in a build failure and system crash.

### Getting CNTK Source code

Before proceeding further, please note, that if you plan on making modifications to the CNTK code you should read the information on [Developing and Testing](./Developing-and-Testing.md) in this Wiki.

Use [Git](./Setup-CNTK-on-Linu#git) to clone the CNTK Repository and access the source code:
```
git clone https://github.com/Microsoft/cntk
cd cntk
git submodule update --init -- Source/Multiverso
```

Submodule Multiverso is used for enable [DataParallelASGD](./Multiple-GPUs-and-machines#24-data-darallel-training-with-parameter-server) for training.

**Optional** If you don't need DataParallelASGD support, then pass the option `--asgd=no` to the configure command.


**IMPORTANT!** The procedure above does not provide you with the access to 1-bit Stochastic Gradient Descent (1bit-SGD) and [BlockMomentumSGD](./Multiple-GPUs-and-machines#22-block-momentum-sgd) components. If you want to build CNTK with this functionality enabled, please read the instructions on [this page](./Enabling-1bit-SGD) and then proceed with the installation.

### Building CNTK

To build CNTK use the following commands (we assume that the CNTK repository was cloned to `~/Repos/cntk`):
```
cd ~/Repos/cntk
mkdir build/release -p
cd build/release
../../configure
```
Ensure that the `configure` output corresponds to the packages you installed in the previous sections. I.e. ensure that `configure` finds CUDA if installed, etc.

Do the following to build cntk using all cores to minimize build time.  Note that on some computer, this can overwhelm your system leading to hangs or breaks during the build.
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

### Quick test of CNTK build functionality

To ensure that CNTK is working properly in your system, you can quickly run an example from the
[Hello World - Logistic Regression](https://github.com/Microsoft/CNTK/tree/master/Tutorials/HelloWorld-LogisticRegression) tutorial.
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

#### Trying CNTK with GPU

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

### Contributing to CNTK code

If you plan modifications to the code you should read the information on [Developing and Testing](./Developing-and-Testing.md) in this Wiki.

**Next steps**
* [Enabling 1bit SGD](./Enabling-1bit-SGD.md)
* [Developing and Testing](./Developing-and-Testing.md)
* [CNTK usage overview](./CNTK-usage-overview.md)
* [Examples](./Examples.md)
