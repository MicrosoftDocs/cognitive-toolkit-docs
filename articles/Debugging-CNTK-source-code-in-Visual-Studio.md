---
title:   Debugging CNTK's GPU source code in Visual Studio
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/18/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Debugging CNTK's GPU source code in Visual Studio

To debug CNTK's mainline CPU source code, follow the steps below.  To additionally debug the **CUDA code for GPUs** in CNTK, follow the steps below first, and then click [here](./Debugging-CNTKs-GPU-source-code-in-Visual-Studio.md) for further steps.

In Launch Visual Studio, and load the cntk.sln solution.
In the **Solution Explorer**, find the CNTK project and make sure it is the startup project (it should be bolded).  If it is not, right click on the project in the **Solution Explorer** and choose **Set as StartUp Project**.

In the **Solution Explorer**, find the CNTK project and right click on **Properties**.  From the **Properties** dialog, click on **Configuration Properties** and then on **Debugging**.

Assuming you have your CNTK source at `C:\src` and you want to debug with config file `lr_bs.cntk` from the the tutorial
[HelloWorld-LogisticRegression](https://github.com/Microsoft/CNTK/tree/release/latest/Tutorials/HelloWorld-LogisticRegression), set the
Command Arguments as follows:

`configFile=lr_bs.cntk deviceId=auto makeMode=false`

In addition, set the Working Directory field as follows:

`C:/src/cntk/Tutorials/HelloWorld-LogisticRegression`

If you have your CNTK source somewhere else or you want to debug a different config file, make the appropriate changes

Set your build target as "Debug"

Build and run.
 
