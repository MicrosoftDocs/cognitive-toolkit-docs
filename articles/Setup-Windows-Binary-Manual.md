---
title:   Setup Windows binary manual
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   quickstart
ms.devlang:   brainscript, python, cpp
---
# Setup Windows binary manual

## Windows manual installation from binary distribution

This page will walk you through the process of manual installing the Microsoft Cognitive Toolkit (CNTK) based on a binary distribution. It is intended for those users who want to have a customized installation. The recommended way to install the CNTK binaries is through the [scripted installation](./Setup-Windows-Binary-Script.md) which also gives you the possibility to control the installation by setting [optional parameters](./Setup-Windows-Binary-Script-Options.md).

If you are looking for any other kind of support to setup a CNTK build environment or installing CNTK on your system, you should go [here](./Setup-CNTK-on-your-machine.md) instead.

If you previously installed an earlier version of the CNTK Python package, you can jump to step 3 to update existing CNTK package installation from your Python environment

**Step 1**: Install prerequisites

Download the appropriate binary package from [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). 
The installation described on this page only works with the binary package. The
source code package, which is also available from the CNTK Release page, is NOT required.

After you followed the step above, you can now unpack the zip file into a local folder of your 
choice, although in the following text we will assume you unpacked the package into `c:\local`. If you installed the 
zip into a different directory, please adjust the commands given below accordingly.

CNTK on Windows requires the following prerequisites to be installed on your system. Please install them from the links below: 

- The Visual C++ Redistributable Package for Visual Studio 2017 can be installed from `c:\local\cntk\prerequisites\VS2017\VC_redist.x64.exe`

- Microsoft MPI Version 7 (7.0.12437.6) can be installed from `c:\local\cntk\prerequisites\MsMPiSetup.exe`. At this point we suggest this 
version of MSMPI (7.0.12437.6), as this is the version which is currently tested by us in connection with the Microsoft Cognitive Toolkit.

- For GPU systems ensure that you have the [latest NVIDIA driver](http://www.nvidia.com/drivers)

**Step 2**: CNTK setup for Python

Please follow [these instructions](./Setup-Windows-Python.md) to set up CNTK for Python on your machine.
