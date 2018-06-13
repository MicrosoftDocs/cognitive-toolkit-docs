---
title:   Setup CNTK Python Tools For Windows
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   python 
---

# Setup CNTK Python Tools For Windows

If you want to use Python Tools for Visual Studio (PTVS):
* First determine your path to Visual studio (like: "c:\Program Files (x86)\Microsoft Visual Studio 14.0").  Take that path and create an environment variable named MYVSPATH.  `set MYVSPATH=thePathToVSDescribedAbove`.
* If you have a source install, make sure you have set PYTHONPATH from [here](./Setup-CNTK-on-Windows.md#pythonpath):
* Next get the path to your CNTK installation:
  * If you have a binary install, (take a look in c:\local). For example if you installed CNTK 2.5.1, you would have a path like: c:\local\CNTK-2-5-1-Windows-64bit-GPU.  Take that path and create an environment variable named MYCNTKPATH:  `set MYCNTKPATH=thePathToCNTKDescribedAbove`.
  * If you have a source install, find the path just above where you cloned.  That is just above the cntk dir. Take that path and create an environment variable named MYCNTKPATH: `set MYCNTKPATH=thePathToCNTKDescribedAbove`.
* Next Setup your environment with `%MYVSPATH%\vc\vcvarsall.bat amd64`.
* Next depending on your install type:
  * If you have a binary install update your PATH environment with `set PATH=%MYCNTKPATH%\cntk\cntk;%PATH%`.
  * If you have a source install, and built Release, do  `set PATH=%MYCNTKPATH%\cntk\x64\Release;%PATH%`. If you built something different than Release, specify that instead.
* Then open Visual Studio with `%MYVSPATH%\Common7\IDE\devenv.exe`
* In VS, go to Tools -> Python Tools -> Python Environments and create a new environment (by clicking on the "+Custom" button).
* Select Configure from the dropdown menu and set the prefix path to the environment dir inside Anaconda.  If you did a binary install, this path is likely to be:
  `C:\local\Anaconda3-4.1.1-Windows-x86_64\envs\cntk-py35\`.
* Afterwards, click Auto Detect and the rest of the entries will be filled out automatically.
* In Visual Studio, create a new Empty Python project
* Add in your Python file(s).
* If you have a source install, to get IntelliSense, add in the full Python lib dir as a part of your project at: MYCNTKPATH%\CNTK\bindings\python

**Links**
* [Setup CNTK on Windows](./Setup-CNTK-on-Windows.md)
* [Examples](./Examples.md)
