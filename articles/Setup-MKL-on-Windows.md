---
title:   Setup MKL on Windows
author:    thiagofc
ms.author:   thiagofc
ms.date:   02/15/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   python
---

# Setup MKL on Windows

This section outlines the packages you need to setup in order for CNTK to leverage Intel MKL library.

CNTK supports using the Intel MKL via a custom library version [MKLML](https://github.com/01org/mkl-dnn/releases).

Installing the MKLML library: 

* Create a directory on your machine to hold MKLML, e.g.:

```
    mkdir c:\local\mklml_win_2018.0.3.20180406
```

* Download the file [mklml_win_2018.0.3.20180406.zip](https://github.com/intel/mkl-dnn/releases/download/v0.14/mklml_win_2018.0.3.20180406.zip). Unzip it into the local folder without the versioned sub directory.

* Quick installation check: If you followed the instruction above and used the same paths, the command `dir c:\local\mklml_win_2018.0.3.20180406\lib\mklml.dll` will succeed.
