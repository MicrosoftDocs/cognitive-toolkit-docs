---
title:   Setup Windows binary script options
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Setup Windows binary script options

## CNTK binary installation script options

The Windows binary installations script can be run with several options to adapt the installation to your specific needs. Standard usage of the script is explained [here](./Setup-Windows-Binary-Script.md). On this page we will explain the optional installation options.

**Note:** If you have an existing Anaconda environment that you want to install CNTK to, take a look at the AnacondaBasePath option below.

By default the script will:

 - Create or reuse Anaconda3 in the folder `C:\local\Anaconda3-4.1.1-Windows-x86_64`
 - Create or update a CNTK Python 3.5 environment in `C:\local\Anaconda3-4.1.1-Windows-x86_64\envs\cntk-py35`

Overview:
```
install.bat 
  [-AnacondaBasePath <LOCAL_DIRECTORY>]
  [-PyVersion (27|34|35|36)]
  [-NoConfirm]
  [-Execute]
```
### Parameter `AnacondaBasePath`

`install.bat -AnacondaBasePath <LOCAL_DIRECTORY>`

This optional parameter allows you to specify the location of an Anaconda installation to be used or created on your machine. If the directory exists on your machine, the script will continue under the assumption that this is a working Anaconda 3 (4.1.1) (or compatible) installation, and will create the CNTK Python environment in that location. If the directory doesn't exist, a new Anaconda installation will be created in the specified location.

Example:
```
install.bat -AnacondaBasePath c:\Python\Anaconda3
```

### Parameter `PyVersion`

This is an optional parameter and can be used to specify the Python version used in the CNTK Python environment. Supported values for this parameter are 27, 34, 35, or 36. The default values is 35 (for a CNTK Python 35 environment).

`PyVersion` influences the result of the installation by

- The Python CNTK environment matching the selected `PyVersion` is created in the directory `<AnacondaBasePath>\envs\cntk-py<PyVersion>`.
- The `cntkpy<PyVersion>.bat` file is created in the scripts directory to activate the Python environment

Example:
```
install.bat -PyVersion 34
```

### Parameter `Execute`

If you just want to see what components would be installed, you can just set the parameter `-Execute:$false`. The installation script will execute, but no changes to your system will be performed.

Example:
```
install.bat -Execute:$false
```

### Parameter `NoConfirm`

By default the script will ask you to confirm before the installation process starts. If you supply this parameter, the script will start the installation without any confirmation questions.

Example:
```
install.bat -NoConfirm 
```

### Standard parameters

The main installation is being done by a PowerShell script. PowerShell supports several standard parameters for script files which can be used. In case of problems with the installation and to report problems it could be helpful to run the script with the `-Verbose` option. This can make it easier to analyze installation issues.

Example:
```
install.bat -Verbose
```

Links:

- [CNTK binary installation with scripts on Windows](./Setup-Windows-Binary-Script.md)
- [Setup CNTK on your machine](./Setup-CNTK-on-your-machine.md)
- [Home](./index.md)
