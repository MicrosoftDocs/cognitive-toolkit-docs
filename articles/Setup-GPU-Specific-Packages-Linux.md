---
title:   Setup GPU Specific packages on Linux
author:    thiagofc
ms.author:   thiagofc
ms.date:   02/20/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   python
---

# Setup GPU specific packages on Linux 

This section outlines the packages you need to setup in order for CNTK to leverage NVIDIA GPUs.

## Checking your GPU compatibility and getting the latest driver

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
sudo chmod +x ./NVIDIA-Linux-x86_64-384.111.run
sudo ./NVIDIA-Linux-x86_64-384.111.run
```

We recommend accepting the default installation options

Note that the driver installation program may complain about nouveau kernel driver. Refer to your platform documentation for instructions for disabling it. For Ubuntu you may use [this set of instructions](http://askubuntu.com/a/451248).

* If stopped during the steps above, start X Windows manager. Refer to your platform documentation for the exact commands.

Example: for Ubuntu, use the following command (in case of `lightdm` as your X Windows manager):

```
sudo start lightdm
```

## CUDA 9

The procedure below explains how to install CUDA using `.run` file distribution. You can also use `DEB` or `RPM` packages installation. You will find the package for your system at [NVIDIA CUDA 9.0 Download page](https://developer.nvidia.com/cuda-90-download-archive) and installation instructions in [CUDA Online Documentation](http://docs.nvidia.com/cuda/pdf/CUDA_Installation_Guide_Linux.pdf).

Download and install the NVIDIA CUDA 9.0 Toolkit:

* Find the `.run` file for your platform [here](https://developer.nvidia.com/cuda-90-download-archive) and download it.

* If your X Windows manager is running, the installation will likely fail. Open a remote terminal session to your machine and stop the X Windows manager. Refer to your platform documentation for the exact commands.

Example: for Ubuntu use the following command (in case of `lightdm` as your X Windows manager):

```
sudo stop lightdm
```

* Install the CUDA 9.0 Toolkit (note that the `.run` file name may be different for your system):

```
chmod +x ./cuda_9.0.176_384.81_linux.run
sudo ./cuda_9.0.176_384.81_linux.run
```

When prompted by the installer:

```
Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81?
(y)es/(n)o/(q)uit:
```

Select `no` if you have already installed the latest driver by performing the steps in the [previous section](#checking-your-gpu-compatibility-and-getting-the-latest-driver). If you have not done it, select `yes`, but we **strongly recommend** updating to the latest driver after installing CUDA toolkit.

If you declined the driver installation from the CUDA 9.0 package, you will get the following warning at the end of the installation:

```
***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 384.00 is required for CUDA 9.0 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run -silent -driver
```

Ignore this warning. 

If stopped during the steps above, start X Windows manager. Refer to your platform documentation for exact commands.

**Example:** for Ubuntu use the following command (in case of `lightdm` as your X Windows manager):

```
sudo start lightdm
```

Add the following environment variable to your current session and your `.bashrc` profile (if you modified the default paths during the CUDA installation, change the values below accordingly):

```
export PATH=/usr/local/cuda-9.0/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH
```

The next step is optional. You may skip further to the [next section](#gpu-deployment-kit).

### OPTIONAL. Verifying CUDA 9.0 installation

You may verify your CUDA installation by compiling the CUDA samples (we assume the default paths were used during the CUDA installation). Note that building all samples is a lengthy operation:

```
cd ~/NVIDIA_CUDA-9.0_Samples/
make
```

After the successful build invoke `deviceQuery` utility:

```
~/NVIDIA_CUDA-9.0_Samples/1_Utilities/deviceQuery/deviceQuery
```

If everything works well, you should get an output similar to the one below:

```
./deviceQuery Starting...

 CUDA Device Query (Runtime API) version (CUDART static linking)

Detected 1 CUDA Capable device(s)

Device 0: "GeForce GTX 960"
  CUDA Driver Version / Runtime Version          9.0 / 9.0
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

deviceQuery, CUDA Driver = CUDART, CUDA Driver Version = 9.0, CUDA Runtime Version = 9.0, NumDevs = 1, Device0 = GeForce GTX 960
Result = PASS
```

## GPU Deployment Kit

Starting from CUDA version 8, the GPU Deployment Kit is a part of the CUDA package and is no longer required to be installed separately.

### cuDNN

Install [NVIDIA CUDA Deep Neural Network library (cuDNN)](https://developer.nvidia.com/cuDNN).

> [!IMPORTANT]
> If you previously installed cuDNN for an older version make sure that you upgrade to the CUDA 9.0 compatible version

> [!IMPORTANT]
> Install cuDNN using the exact version and target path as specified below. This is necessary because it is expected by the CNTK build configuration program.

* Use the following commands:

```
wget https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/libcudnn7_7.0.4.31-1+cuda9.0_amd64.deb
sudo dpkg -i libcudnn7_7.0.4.31-1+cuda9.0_amd64.deb
```

### OPTIONAL. NCCL

[NVIDIA's NCCL library](https://github.com/NVIDIA/nccl) provides optimized primitives for collective
multi-GPU communication on Linux.
CNTK can take advantage of these accelerated primitives for parallel jobs running on a single host
(cf. [here](./Multiple-GPUs-and-machines.md) for an introduction into parallel training with CNTK).

Please follow instructions [here](https://github.com/NVIDIA/nccl) to download the NVIDIA NCCL library.
```
wget https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/libnccl-dev_2.1.2-1+cuda9.0_amd64.deb
wget https://developer.download.nvidia.com/compute/machine-learning/repos/ubuntu1604/x86_64/libnccl2_2.1.2-1+cuda9.0_amd64.deb
sudo dpkg -i libnccl2_2.1.2-1+cuda9.0_amd64.deb libnccl-dev_2.1.2-1+cuda9.0_amd64.deb
```

Then, use the CNTK configure option `--with-nccl=<path>` to enable building with NVIDIA NCCL.
For example, if NCCL are installed in folder other than the default folder `/usr`, use
`configure --with-nccl=<nccl install folder>` (plus additional options) to build with NVIDIA NCCL support.

> [!NOTE]
> Currently, CNTK's support for NVIDIA NCCL is limited to data-parallel SGD with 32/64 gradient bits, using
the CNTK binary. Support for additional parallelization methods and CNTK v2 will be added in the future.
> The official release of CNTK is built with NCCL enabled. All linux Python wheels already include NCCL binary. 
For Brainscript users on Linux, NCCL needs to be installed.
> If user prefers to not use NCCL, please build CNTK from source. Note that configure automatically detects NCCL 
installed under /usr, so please uninstall NCCL before build.

### CUB

If you are installing CNTK for Python, you may skip to the [next section](./Setup-Linux-Python.md#mkl). Otherwise proceed further.

Get and install NVIDIA CUB using the commands below.

> [!IMPORTANT]
> Install NVIDIA CUB using the exact version and target path as specified below. This is necessary because it is expected by the CNTK build configuration program.

Use the following commands:

```
wget https://github.com/NVlabs/cub/archive/1.7.4.zip
unzip ./1.7.4.zip
sudo cp -r cub-1.7.4 /usr/local
```
