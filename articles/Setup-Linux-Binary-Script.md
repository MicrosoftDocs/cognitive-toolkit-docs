---
title:   Setup Linux Binary Script
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   NA
---

# Setup Linux Binary Script

## CNTK as a Docker container
Before moving any further you may consider deploying CNTK as a prebuilt Docker container from Docker Hub. Read the [corresponding section](./CNTK-Docker-Containers.md).

## CNTK binary installation with scripts on Linux

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK)
based on a binary distribution we have prepared and you can download from our website. It is an easy way
to get you up-and-running quickly. 

You can find an overview about all the available installation options for CNTK on [this page](./Setup-CNTK-on-your-machine.md).

We will install the CNTK binaries, the CNTK prerequisites, and create (or update) a Python 2.7, 3.5, or 3.6 environment 
on your computer. The changes are as much localized as possible to not impact any other installed
software. _**If you have already installed a previous version of CNTK2 on your machine, the script will 
update this installation.**_

Please follow the steps below to install the binaries. The installation script
will additionally download the necessary dependencies, so an Internet
connection is required when running the script.

The script was tested on Ubuntu 14.04 and 16.04 *only*. It will generate a warning about possible failures if run on any other platform.

**Step 1**: Download the appropriate binary package from [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). Unpack the tar.

Note: Choose a GPU binary download *only* if your machine has an NVidia GPU.

**Step 2**: Run bash Installation script

Below we assume that you have unpacked the CNTK Binary package to `/home/username`.
Please use the following commands, depending on your preferred CNTK Python version:

- Run these commands to install a CNTK Python 3.5 based environment:
  ```
  cd /home/username/cntk/Scripts/install/linux
  ./install-cntk.sh
  ```
- The script also supports installing a Python 2.7 or Python 3.6 based CNTK environment. You can do this by adding the value `27` or `36` to the optional parameter `--py-version` to the command, e.g., to run these commands to install a CNTK Python 3.5 based environment:
  ```
  cd /home/username/cntk/Scripts/install/linux
  ./install-cntk.sh --py-version 35
  ```
- The script also allows to customize the location of the Anaconda installation, or use an existing
  Anaconda installation. Use the option `--anaconda-basepath <path>` to specify an Anaconda installation
  path. If the user-provided path doesn't exist, the script will create it and install Anaconda into it.
  For example:
  ```
  cd /home/username/cntk/Scripts/install/linux
  ./install-cntk.sh --anaconda-basepath /usr/local/anaconda3
  ```
 
The script will download several installation packages from remote locations.
Execution will take some time (expect at least 20 minutes on Ubuntu 16.04 and even more on Ubuntu 14.04, depending on the packages required on your system).

By the end of the successful setup the script will inform you about the location of the CNTK Python environment script and of the location of CNTK Tutorials and Examples.

- For **GPU Systems**: Please ensure that you have the [latest NVIDIA driver](http://www.nvidia.com/drivers)

**Step 3**: Verify the setup (Python)

- **Activate CNTK environment** by executing the command specified by the Installation script (see previous step). In our example it will be:
  ```
  source "/home/username/cntk/activate-cntk"
  ```

- **Run an example** from `Tutorials` directory to verify your installation. Run `python NumpyInterop/FeedForwardNet.py`. You should see the following output on the console:
  ```
  Minibatch[   1- 128]: loss = 0.564038 * 3200
  Minibatch[ 129- 256]: loss = 0.308571 * 3200
  Minibatch[ 257- 384]: loss = 0.295577 * 3200
  Minibatch[ 385- 512]: loss = 0.270765 * 3200
  Minibatch[ 513- 640]: loss = 0.252143 * 3200
  Minibatch[ 641- 768]: loss = 0.234520 * 3200
  Minibatch[ 769- 896]: loss = 0.231275 * 3200
  Minibatch[ 897-1024]: loss = 0.215522 * 3200
  Finished Epoch [1]: loss = 0.296552 * 25600
  error rate on an unseen minibatch 0.040000
  ```

- **Run the Jupyter notebooks**, which contain several tutorials, by executing the following commands:
  ```
  cd /home/username/cntk/Tutorials
  jupyter notebook
  ```
> This will spawn a browser with all available notebooks ready to be run. Should the notebooks fail to execute, run `conda install jupyter` from the activated CNTK Python environment.
