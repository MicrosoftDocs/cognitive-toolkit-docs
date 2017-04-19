## Linux binary manual installation

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK)
based on a binary distribution we have prepared and you can download from our website. 

> These instructions apply to release 2.0.rc1.

Note: You can find an overview about all the available installation options for CNTK on [this page](./Setup-CNTK-on-your-machine).

We will install the CNTK binaries, the CNTK prerequisites, and create (or update) a Python 2.7, 3.4, or 3.5 environment 
on your computer. The changes are as much localized as possible to not impact any other installed
software.

>Note: If you previously installed an earlier version of the CNTK Python package, you can skip steps 1 
through 3 below and directly jump to step 4 to update your existing CNTK package installation from your 
Python 2.7, 3.4, 3.5 environment

**Step 1**: Download and install pre-requisites

**Docker** users please follow the instructions [here](https://github.com/Microsoft/CNTK/wiki/CNTK-Docker-Containers). 
Others please continue reading.

CNTK V2 on Linux requires the following prerequisites to be installed from the links below: 

- [C++ Compiler](https://github.com/Microsoft/CNTK/wiki/Setup-CNTK-on-Linux#c-compiler)
- [Open MPI](https://github.com/Microsoft/CNTK/wiki/Setup-CNTK-on-Linux#open-mpi)
**IMPORTANT!** We strongly recommend to follow Open MPI installation procedure described by the link above to ensure the correct work of CNTK.
- For GPU systems ensure that you have the [latest NVIDIA driver](http://www.nvidia.com/drivers)

Download the required binary package from [CNTK Releases page](../releases) and extract it to your machine.

**Step 2**: CNTK setup for Python

Please follow [these instructions](./Setup-Linux-Python) to set up CNTK for Python on your machine.

**Step 3**: CNTK setup for BrainScript

- Set the following environment variables (we assume that the CNTK archive is extracted to ```/home/username/cntkbin```):
```
export PATH=/home/username/cntkbin/cntk/bin:$PATH
export LD_LIBRARY_PATH=/home/username/cntkbin/cntk/lib:/home/username/cntkbin/cntk/dependencies/lib:$LD_LIBRARY_PATH
```
Note: If you've built openmpi yourself (for example, on Ubuntu 14), make sure to add its library directory to `LD_LIBRARY_PATH` as well:
```
export LD_LIBRARY_PATH=[path-to-your-openmpi-installation]/lib:$LD_LIBRARY_PATH
```

To validate, please perform the following commands:
```
cd /home/username/cntkbin/Tutorials/HelloWorld-LogisticRegression
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
