---
title:   Setup Windows binary script
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   NA
---

# Setup Windows binary script

## CNTK binary installation with scripts on Windows

This page will walk you through the process of installing the Microsoft Cognitive Toolkit (CNTK) for Windows based on a binary distribution.

If you are looking for any other kind of support in setting up a CNTK build environment or installing CNTK on your system, you should go [here](./Setup-CNTK-on-your-machine.md) instead. 

> These instructions apply to release 2.5.

**Step 1: Download and prepare the Microsoft Cognitive Toolkit**

Download the appropriate binary package from [CNTK Releases page](https://github.com/Microsoft/CNTK/releases). The installation described on this page only works with the binary package. The source code package, which is also available from the CNTK Release page, is NOT required.

Unpack the zip file into an empty local folder of your choice. We recommend you name the folder according to the CNTK version, so you don't mix files of a previously downloaded CNTK version with the newly downloaded files. In the following we will assume you unzipped the files into the directory `c:\local\CNTK-2-5`. If you used another directory, adjust the following instructions accordingly.

**Step 2: Run the installation script**

Start a standard windows command prompt, and run the installation script:
```
cd c:\local\CNTK-2-5
cd cntk\Scripts\install\windows
install.bat
```

The script will inspect your system and determine the prerequisites which are missing. You will be notified about the proposed installation steps and locations. This can be adjusted by optional parameters to the install script, [see here](./Setup-Windows-Binary-Script-Options.md).

The script will download needed components from the web, a connection to the Internet is required. It will execute these components, and according to the setting of UAC (User Account Control) you will be asked to acknowledge the execution and installation of these components.

Depending on the speed of your system, the Internet connection, and the required components to install, the whole process can take up to 20 minutes. Especially the Anaconda3 install is very time consuming, please be patient.

By the end of the successful execution of the installation script:
 - VS2017 Runtime will be installed
 - MSMPI will be installed
 - Anaconda3 will be installed into the folder `C:\local\Anaconda3-4.1.1-Windows-x86_64`
 - A CNTK-PY35 environment will be created or updated in `C:\local\Anaconda3-4.1.1-Windows-x86_64\envs`
 - The CNTK Python module will be installed or updated in the created CNTK-PY35 environment
 - A batch file will be created to activate the created Python environment and set the required environment variables

**Step 3: Update your GPU Driver**

If you downloaded/installed the GPU version of CNTK and you are using a NVidia graphics card, ensure that you have the [latest NVIDIA driver](http://www.nvidia.com/drivers) installed.

**Step 4: Verify the setup from Python**

To verify the correct installation of CNTK from Python:

You need to **activate** the CNTK environment by opening a standard Windows command prompt and running the batch file created by the installation script (see Step 2). This script will add Python and the CNTK installation to your current PATH environment only for the current Windows command prompt.

```
cd c:\local\CNTK-2-5
cd scripts
cntkpy35.bat
```

> [!NOTE]
> You will need to activate the CNTK environment every time you plan on using the CNTK Python environment.

You can now follow the [standard description](./Setup-Test-Python.md) to test your installation from Python and run the tutorials or Jupyter notebooks.

- If you want to use Python Tools, CNTK, and Visual Studio Code, see this [blog](http://dacrook.com/cntk-vs-code-awesome/). 
- If you want to use Python Tools, CNTK, and Visual Studio, see [here](./Setup-CNTK-Python-Tools-For-Windows.md)   

Next:

- [CNTK binary installation scripts options](./Setup-Windows-Binary-Script-Options.md)
- [Setup CNTK on your machine](./Setup-CNTK-on-your-machine.md)
- [Home](./index.md)
