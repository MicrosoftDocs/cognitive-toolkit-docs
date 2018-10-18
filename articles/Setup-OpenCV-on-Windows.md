---
title:   Setup OpenCV on Windows
author:    thiagofc
ms.author:   thiagofc
ms.date:   02/15/2018
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   quickstart
ms.devlang:   python
---

# Setup OpenCV on Windows 

CNTK 2.2 requires [Open Source Computer Vision (OpenCV)](http://opencv.org/) to be installed but it is optional for CNTK 2.3+.

You need to install OpenCV for CNTK 2.3+, if you want to use the following components:
- CNTK Image Reader
- CNTK Image Writer - required to use Tensorboard's Image feature.

Download and install version 3.1 for Windows from [this download location](http://sourceforge.net/projects/opencvlibrary/files/opencv-win/3.1.0/opencv-3.1.0.exe/download). This installer is just an unpacker. E.g. enter "c:\local" under the "Extract to". We recommend that you rename the created OpenCV folder to reflect the version number you just installed (Opencv3.1.0). 

Quick installation check: If you followed the instruction above and used the same paths, the command `dir C:\local\opencv3.10\build\x64\vc14\bin\opencv_world310.dll` will succeed.
