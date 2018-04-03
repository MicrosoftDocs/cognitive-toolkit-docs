---
title:   CNTK Python known issues and limitations
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/03/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   python, cpp, csharp, dotnet
---

# CNTK Python known issues and limitations

- The core API itself is implemented in C++ for speed and efficiency and Python bindings are created through SWIG. We are increasingly creating thin Python wrappers for the APIs to attach docstrings to, but this is a work in progress and for some of the APIs, you may directly encounter SWIG generated API definitions (which are not the prettiest to read).
