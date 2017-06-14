---
title:   Setup Windows Devinstall Script Option
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   02/16/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Setup Windows Devinstall Script Option

## CNTK developer install script options

The developer installation script (devInstall.ps1) is a PowerShell script located in the `Tools` subdirectory inside the root of the CNTK repository. The script can be run with several options to adopt the installation to your specific needs. Standard usage of the script is explained [here](./Setup-CNTK-with-script-on-Windows.md). On this page we will list the additional installation options. You can get an overview about all the available options by executing `get-help -full .\devInstall.ps1` from inside a PowerShell command window in the directory containing the installation script.

**Note:** If you have an existing Anaconda environment that you want to install CNTK to, take a look at the `AnacondaBasePath` option below.

Overview:
```
./devInstall.ps1 
  [-Execute]
  [-AnacondaBasePath <DIRECTORY>]
  [  
     [-NoPythonEnvironment]
  |
     [-PyEnvironmentName <NAME> ]
     [-PyVersion (27|34|35|36)] 
  ]
  [-localCache <DIRECTORY>]
  [-InstallLocation <DIRECTORY>]
  [-NoConfirm]
```

Please note, if you specify `NoPythonEnvironment`, you can't specify the `PyVersion` or the `PyEnvironmentName` parameter.

### Parameter `AnacondaBasePath`

This optional parameter allows you to specify the location of an Anaconda installation to be used or created on your machine. 

If you don't specify a directory through this parameter, Anaconda will be installed in `C:\local\Anaconda3-4.1.1-Windows-x86_64`. If the (default or by you specified) directory exists, the script will continue under the assumption that the directory contains a working Anaconda 3 (4.1.1) (or compatible) installation. The script will create the CNTK Python environment in that location (unless the parameter `NoPythonEnvironment` is specified).

Example:
```
./devInstall.ps1 -AnacondaBasePath c:\Python\Anaconda3

./devInstall.ps1 -AnacondaBasePath c:\Python\Anaconda3 -NoPythonEnvironment

./devInstall.ps1 -Execute -AnacondaBasePath c:\Python\Anaconda3 -PyVersion 27 -PyEnvironmentName MyCntkEnv
```

### Parameter `NoPythonEnvironment`

This is an optional parameter. If you specify this parameter, no CNTK Python environment will be created inside the `AnacondaBasePath`. If you specify this parameter, the parameter `PyVersion` and `PyEnvironmentName` can not be defined.

Example:
```
./devInstall.ps1 -NoPythonEnvironment
```

### Parameter `PyVersion`

This is an optional parameter and can be used to specify the Python version used in the CNTK Python environment. Supported values for this parameter are 27, 34, 35, or 36. The default values is 35 (for a CNTK Python 35 environment).

`PyVersion` influences the result of the installation by

- The Python CNTK environment matching the selected `PyVersion` is created in the directory `<AnacondaBasePath>\envs\cntk-py<PyVersion>`.
- The `cntkpy<PyVersion>.bat` file is created in the scripts directory to activate the Python environment

Example:
```
./devInstall.ps1 -PyVersion 34
```

### Parameter `PyEnvironmentName`

This optional parameter allows to specify the name of the CNTK Anaconda environment that will be created during the installation process. By default the environment will be named `cntkdev-py<PyVersion>`, where PyVersion is being replaced by the content of the <PyVersion> parameter to this script. If this parameter is specified by you, no version substitution in the environment will be performed. 

Example:
```
./devInstall.ps1 -PyEnvironmentName CntkEnvironment
```

### Parameter `Execute`

If you just want to see what components would be installed, you can just set the parameter `-Execute:$false`. The installation script will execute, but no changes to your system will be performed.

### Parameter `NoConfirm`

By default the script will ask you to confirm before the installation process starts. If you supply this parameter, the script will start the installation without any confirmation questions.

### Parameter `localCache`

The installer will download required files from the internet. These files are reused by the installer if possible. By default the downloaded files will be stored in the directory `c:\installCacheCntk`. You can specify another directory to store these files with this optional parameter.

Example:
```
./devInstall.ps1 -localCache c:\users\currentUser\myCache -Execute
```

### Parameter `InstallLocation`

By default the majority of the installed files will be placed inside the `c:\local` directory. If you prefer a different location you can specify a installation directory using this optional parameter.

Example:
```
./devInstall.ps1 -InstallLocation c:\buildCntk -Execute
```

### PowerShell standard parameters

PowerShell supports several standard parameters for script files. In case of problems with the installation and to report problems it could be helpful to run the script with the `-Verbose` option. This can make it easier to analyze installation issues.

Example:
```
./devInstall.ps1 -Execute -Verbose
```

Links:

- [Setup CNTK on your machine](./Setup-CNTK-on-your-machine.md)
- [Home](./index.md)
