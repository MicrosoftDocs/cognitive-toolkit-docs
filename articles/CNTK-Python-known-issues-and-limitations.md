---
title:   CNTK Python known issues and limitations
author:    chrisbasoglu
date:    04/03/2017
ms.author:   cbasoglu
ms.date:   04/03/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   python, cpp, csharp, dotnet
---

# CNTK Python known issues and limitations

- The core API itself is implemented in C++ for speed and efficiency and python bindings are created through SWIG. We are increasingly creating thin python wrappers for the APIs to attach docstrings to, but this is a work in progress and for some of the APIs, you may directly encounter SWIG generated API definitions (which are not the prettiest to read).
