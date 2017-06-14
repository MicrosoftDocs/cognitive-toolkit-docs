---
title:   Setup Windows binary manual
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   brainscript, python, cpp
---

# Setup Windows binary manual

## Windows manual installation from binary distribution

This page will walk you through the process of manual installing the Microsoft Cognitive Toolkit (CNTK) based on a binary distribution. It is intended for those users who want to have a customized installation. The recommended way to install the CNTK binaries is through the [scripted installation](./Setup-Windows-Binary-Script.md) which also gives you the possibility to control the installation by setting [optional parameters](./Setup-Windows-Binary-Script-Options.md).

If you are looking for any other kind of support to setup a CNTK build environment or installing CNTK on your system, you should go [here](./Setup-CNTK-on-your-machine.md) instead.

> Note: These instructions apply to release 2.0.

If you previously installed an earlier version of the CNTK Python package, you can jump to step 3 to update existing CNTK package installation from your Python environment

**Step 1**: Install prerequisites

Download the appropriate binary package from [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). 
The installation described on this page only works with the binary package. The
source code package, which is also available from the CNTK Release page, is NOT required.

After you followed the step above, you can now unpack the zip file into a local folder of your 
choice, although in the following text we will assume you unpacked the package into `c:\local`. If you installed the 
zip into a different directory, please adjust the commands given below accordingly.

CNTK on Windows requires the following prerequisites to be installed on your system. Please install them from the links below: 

- The Visual C++ Redistributable Package for Visual Studio 2015 can be installed from `c:\local\cntk\prerequisites\VS2015\vc_redist.x64.exe`

- Microsoft MPI Version 7 (7.0.12437.6) can be installed from `c:\local\cntk\prerequisites\MsMPiSetup.exe`. At this point we suggest this 
version of MSMPI (7.0.12437.6), as this is the version which is currently tested by us in connection with the Microsoft Cognitive Toolkit.

- For GPU systems ensure that you have the [latest NVIDIA driver](http://www.nvidia.com/drivers)

**Step 2**: CNTK setup for Python

Please follow [these instructions](./Setup-Windows-Python.md) to set up CNTK for Python on your machine.

**Step 3**: CNTK setup for BrainScript

Please add the CNTK binary directory (e.g., `c:\local\CNTK\cntk`) to your path.

To validate, please perform the following commands:
```
cd c:\local\CNTK\Tutorials\HelloWorld-LogisticRegression
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
