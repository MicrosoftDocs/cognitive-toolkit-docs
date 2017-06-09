---
title:   Developing and testing
author:    chrisbasoglu
date:    01/19/2016
ms.author:   cbasoglu
ms.date:   01/19/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   cpp
---

# Developing and testing

Once you have setup the CNTK sources on your machine you should follow the CNTK development practices to change CNTK on your machine and contribute to the project.

If you make modifications to the code please follow the coding guidelines and make sure tests are still passing with your changes in place.

Our source code doesn't include TAB characters for formatting. If you use Visual Studio as your editor, go to Tools|Options|Text Editor|C/C++|Tabs and make sure it is set to Smart Indenting Tab, Indent Size set to 4, and "Insert Spaces" option selected. You can also load the CppCntk.vssettings file (in the CNTK home directory) which contains settings for C++ editor. To import/export the settings, use Tools -> Import and Export Settings... Visual Studio menu option.

Please do not auto-format existing code (Edit -> Advanced -> Format Document/Ctrl+E,D).

For code you write you can use CLANG-FORMAT (see http://clang.llvm.org/) to perform an initial formatting step. A format specification for CLANG-FORMAT (version 3.7) is available in the root of the CNTK repository (.clang-format)

[Coding Guidelines](./Coding-Guidelines.md)

[How to Test](./How-to-Test.md)

