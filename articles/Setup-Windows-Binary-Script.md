---
title:   Setup Windows binary script
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Setup Windows binary script

## CNTK binary installation with scripts on Windows

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK) for Windows based on a binary distribution.

If you are looking for any other kind of support in setting up a CNTK build environment or installing CNTK on your system, you should go [here](./Setup-CNTK-on-your-machine.md) instead. 

> These instructions apply to release 2.0.

**Step 1: Download and prepare the Microsoft Cognitive Toolkit**

Download the appropriate binary package from [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). The installation described on this page only works with the binary package. The source code package, which is also available from the CNTK Release page, is NOT required.

Unpack the zip file into an empty local folder of your choice. We recommend you name the folder according to the CNTK version, so you don't mix files of a previously downloaded CNTK version with the newly downloaded files. In the following we will assume you unzipped the files into the directory `c:\local\CNTK-2-0`. If you used another directory, adjust the following instructions accordingly.

**Step 2 Run the installation script**

Start a standard windows command prompt, and run the installation script:
```
cd c:\local\CNTK-2-0
cd cntk\Scripts\install\windows
install.bat
```

The script will inspect your system and determine the prerequisites which are missing. You will be notified about the proposed installation steps and locations. This can be adjusted by optional parameters to the install script, [see here](./Setup-Windows-Binary-Script-Options.md).

The script will download needed components from the web, a connection to the Internet is required. It will execute these components, and according to the setting of UAC (User Account Control) you will be asked to acknowledge the execution and installation of these components.

Depending on the speed of your system, the Internet connection, and the required components to install, the whole process can take up to 20 minutes. Especially the Anaconda3 install is very time consuming, please be patient.

By the end of the successful execution of the installation script:
 - VS2015 Runtime will be installed
 - MSMPI will be installed
 - Anaconda3 will be installed into the folder `C:\local\Anaconda3-4.1.1-Windows-x86_64`
 - A CNTK-PY35 environment will be created or updated in `C:\local\Anaconda3-4.1.1-Windows-x86_64\envs`
 - The CNTK Python module will be installed or updated in the created CNTK-PY35 environment
 - A batch file will be created to activate the created Python environment and set the required environment variables

**Step 3 Update your GPU Driver**

If you downloaded/installed the GPU or GPU-1bit-SGD version of CNTK and you are using a NVidia graphics card, ensure that you have the [latest NVIDIA driver](http://www.nvidia.com/drivers) installed.

**Step 4 Verify the setup from Python**

To verify the correct installation of CNTK from Python:

You need to **activate** the CNTK environment by opening a standard Windows command prompt and running the batch file created by the installation script (see Step 2). This script will add Python and the CNTK installation to your current PATH environment only for the current Windows command prompt.

> Note: you will need to do this every time you plan to use the CNTK Python environment

```
cd c:\local\CNTK-2-0
cd scripts
cntkpy35.bat
```

You can now follow the [standard description](./Setup-Test-Python.md) to test your installation from Python and run the tutorials or Jupyter notebooks.
  
 - If you want to use Python Tools, CNTK, and Visual Studio Code, see this [blog](http://dacrook.com/cntk-vs-code-awesome/). 
 - If you want to use Python Tools, CNTK, and Visual Studio, see [here](./Setup-CNTK-Python-Tools-For-Windows.md)   
  
**Step 5** Verify the setup for BrainScript

Perform the following command in the CNTK environment command prompt (see previous step):

```
cd c:\local\CNTK-2-0
cd Tutorials\HelloWorld-LogisticRegression
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

Next:
- [CNTK binary installation scripts options](./Setup-Windows-Binary-Script-Options.md)
- [Setup CNTK on your machine](./Setup-CNTK-on-your-machine.md)
- [Home](./index.md)
