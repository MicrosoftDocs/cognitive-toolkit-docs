## The Cognitive Toolkit and CUDA 8

With the release of CNTK v.2.0 Beta 5 (Windows) and CNTK v.2.0 Beta 6 (Linux) the toolkit started supporting NVIDIA CUDA 8.0 as the development toolkit for GPU accelerated applications. This allows the Microsoft Cognitive Toolkit to take advantage of the latest improvements in NVIDIA graphic cards and optimizations.

For a developer this is currently an optional move. You can still do your CNTK development and testing with NVIDIA CUDA 7.5. Here at Microsoft we moved our Cognitive Toolkit development and testing environment to CUDA 8.0. We will not do any further development or testing on CUDA 7.5. In the next few weeks we will make NVIDIA CUDA 8.0 a mandatory requirement for the Microsoft Cognitive Toolkit on Windows and Linux.


## Windows

### CNTK Binary Packages on Windows

The CNTK binary packages you download starting with Beta 5 contain the NVIDIA CUDA 8.0 runtime components. You don't need to download any additional components. We recommend to check the NVIDIA website for the latest set of [drivers](http://www.NVIDIA.com/Download/index.aspx?lang=en-us) for your NVIDIA graphics card.

### Updating the Windows Development Environment from CUDA 7.5 to CUDA 8.0

Setting up a Windows development environment for the Microsoft Cognitive Toolkit from scratch is described [here](./Setup-CNTK-on-Windows.md). If you already have a working development environment for CNTK using CUDA 7.5, you need to follow these steps to upgrade to CUDA 8.0:

#### CUDA 8.0
The NVIDIA CUDA 7.5 development environment and the CUDA 8.0 development environment can live side-by-side on your machine, but you can also deinstall the CUDA 7.5 toolkit.

Download and install the NVIDIA CUDA 8.0 Toolkit:
* Download the required package from [this download page](https://developer.NVIDIA.com/CUDA-downloads)
* Run the installation procedure

Make sure that the following CUDA environment variables are set to the correct path (the NVIDIA CUDA installer will create these for you). Default installation paths are assumed:
```
CUDA_PATH="C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0"
CUDA_PATH_V8_0="C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v8.0"
```

#### cuDNN 5.1 for CUDA 8.0
Install [NVIDIA CUDA Deep Neural Network library (cuDNN)](https://developer.NVIDIA.com/cuDNN). You need the version ***5.1 for CUDA 8.0**. Use these links: [Windows 8, 8.1, 10](http://developer.download.NVIDIA.com/compute/redist/cudnn/v5.1/cudnn-8.0-windows10-x64-v5.1.zip), [earlier Windows versions](http://developer.download.NVIDIA.com/compute/redist/cudnn/v5.1/cudnn-8.0-windows7-x64-v5.1.zip).

* Extract the archive to a folder on your local disk, e.g. to `C:\local\cudnn-8.0-v5.1\` and set the environment variable `CUDNN_PATH` pointing to that location, e.g.: 
```
    setx CUDNN_PATH C:\local\cudnn-8.0-v5.1\CUDA
```
* Quick installation check: If you followed the instruction above and used the same paths, the command `dir C:\local\cudnn-8.0-v5.1\CUDA\include\cudnn.h` will succeed.

#### Latest NVIDIA GPU card driver
Now install the latest driver for your GPU card:
* Select your card and download the driver pack from [this download location](http://www.NVIDIA.com/Download/index.aspx?lang=en-us)

#### Final steps
You can now restart Visual Studio and open the CNTK project. Please rebuild to make sure all components are recreated with the new toolsets. 


## Linux

### CNTK Binary Packages on Linux

The CNTK binary packages you download starting with Beta 6 contain the NVIDIA CUDA 8.0 runtime components. You don't need to download any additional components. We recommend to check the NVIDIA website for the latest set of [drivers](http://www.NVIDIA.com/Download/index.aspx?lang=en-us) for your NVIDIA graphics card.

### Updating the Windows Development Environment from CUDA 7.5 to CUDA 8.0

#### Latest NVIDIA GPU card driver
Install the latest driver for your GPU card:
* Select your card and download the driver pack from [this download location](http://www.NVIDIA.com/Download/index.aspx?lang=en-us)


### CUDA 8.0

NVIDIA recommends deinstalling previous CUDA versions before installing CUDA 8.0 as described [here](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#handle-uninstallation).

We recommend installing CUDA using [Runfiles](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#runfile). Perform download and installation of CUDA 8.0 as described in the link from the previous sentence.

If you performed the upgrade to the latest NVIDIA Card driver before installing CUDA 8.0, answer **"No"** to the following prompt during CUDA 8.0 installation procedure:
```
Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 367.48?
(y)es/(n)o/(q)uit:
```
If you have answered "No" to the prompt above you will likely get the following message at the end of CUDA 8.0 installation:
```
***WARNING: Incomplete installation! This installation did not install the CUDA Driver. A driver of version at least 361.00 is required for CUDA 8.0 functionality to work.
To install the driver using this installer, run the following command, replacing <CudaInstaller> with the name of this run file:
    sudo <CudaInstaller>.run -silent -driver
```

#### cuDNN 5.1 for CUDA 8.0

Install the appropriate version of [NVIDIA CUDA Deep Neural Network library (cuDNN)](https://developer.NVIDIA.com/cuDNN). Installation procedure is described in this [CNTK Wiki Section](https://github.com/Microsoft/CNTK/wiki/Setup-CNTK-on-Linux#cudnn). 

#### Final steps
Now you can rebuild CNTK with CUDA 8.0 and the correspondent version of cuDNN. Ensure you remove existing `Makefile` and rerun `configure` script before rebuilding.