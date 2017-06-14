---
title:   Setup Linux binary script
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Setup Linux binary script

## CNTK as a Docker container
Before moving any further you may consider deploying CNTK as a prebuilt Docker container from Docker Hub. Read the [corresponding section](./CNTK-Docker-Containers.md).

## CNTK binary installation with scripts on Linux

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK)
based on a binary distribution we have prepared and you can download from our website. It is an easy way
to get you up-and-running quickly. 

> Note: These instructions apply to release 2.0.

>Note: You can find an overview about all the available installation options for CNTK on [this page](./Setup-CNTK-on-your-machine.md).

We will install the CNTK binaries, the CNTK prerequisites, and create (or update) a Python 2.7, 3.4, 3.5, or 3.6 environment 
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
- The script also supports installing a Python 2.7, Python 3.5, or Python 3.6 based CNTK environment. You can do this by adding the value `27`, `34`, or `36` to the optional parameter `--py-version` to the command, e.g., to run these commands to install a CNTK Python 3.4 based environment:
  ```
  cd /home/username/cntk/Scripts/install/linux
  ./install-cntk.sh --py-version 34
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

**Step 4 (Optional)**: Verify the setup (BrainScript)

Perform the following command in the CNTK environment command prompt (see previous step):
```
cd /home/username/cntk/Tutorials/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```
The last lines of the CNTK output on the console should look similar to this:
```
Finished Epoch[42 of 50]: [Training] lr = 0.04287672 * 1000; err = 0.01152817 * 1000; totalSamplesSeen = 42000; learningRatePerSample = 0.039999999; epochTime=0.050296s
Finished Epoch[43 of 50]: [Training] lr = 0.04388479 * 1000; err = 0.01206375 * 1000; totalSamplesSeen = 43000; learningRatePerSample = 0.039999999; epochTime=0.052143s
Finished Epoch[44 of 50]: [Training] lr = 0.04223433 * 1000; err = 0.01105073 * 1000; totalSamplesSeen = 44000; learningRatePerSample = 0.039999999; epochTime=0.057235s
Finished Epoch[45 of 50]: [Training] lr = 0.04208072 * 1000; err = 0.01140516 * 1000; totalSamplesSeen = 45000; learningRatePerSample = 0.039999999; epochTime=0.051414s
Finished Epoch[46 of 50]: [Training] lr = 0.04261674 * 1000; err = 0.01158323 * 1000; totalSamplesSeen = 46000; learningRatePerSample = 0.039999999; epochTime=0.051115s
Finished Epoch[47 of 50]: [Training] lr = 0.04326523 * 1000; err = 0.01164283 * 1000; totalSamplesSeen = 47000; learningRatePerSample = 0.039999999; epochTime=0.051611s
Finished Epoch[48 of 50]: [Training] lr = 0.04225255 * 1000; err = 0.01148774 * 1000; totalSamplesSeen = 48000; learningRatePerSample = 0.039999999; epochTime=0.0509s
Finished Epoch[49 of 50]: [Training] lr = 0.04173276 * 1000; err = 0.01124948 * 1000; totalSamplesSeen = 49000; learningRatePerSample = 0.039999999; epochTime=0.049659s
Finished Epoch[50 of 50]: [Training] lr = 0.04399402 * 1000; err = 0.01202178 * 1000; totalSamplesSeen = 50000; learningRatePerSample = 0.039999999; epochTime=0.052725s

COMPLETED.
```
If you have an NVidia GPU and installed a GPU build, you can also try this command:
```
cntk configFile=lr_bs.cntk makeMode=false command=Train deviceId=auto
```
To validate that the GPU was being used, look for the following line in your output:
```
Model has 9 nodes. Using GPU 0.
```
