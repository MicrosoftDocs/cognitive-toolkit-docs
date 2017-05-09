---
title:   Troubleshoot
author:    chrisbasoglu
date:    04/03/2017
ms.author:   cbasoglu
ms.date:   04/03/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Troubleshoot

This page collects some of the most frequent pitfalls users encounter.

## During training of my binary classifier I always see a ClassificationError of 0 even from the first minibatch

ClassificationError only works correctly for multiclass problems. For binary classification please use [this simple recipe](./How-do-I-Express-Things-in-BrainScript.md#express-the-error-rate-of-my-binary-classifier)

## Although the model was trained with a larger set than the evaluation set, CNTK runs out of memory during evaluation.

Training the model usually has a `minibatchSize` property set in its CNTK configuration file. When evaluating the model using CNTK.exe, ensure that the `minibatchSize` is appropriate. To quickly determine if this property is causing the issue, set the property to a low value (e.g. `minibatchSize=2`) in the configuration file for the evaluation command. (cf. Issue #468)

## I am getting SegFault on Linux with deep networks (like ResNet150).
You may not have enough stack size.  BrainScript requires a lot of stack.  Run the command: ulimit -s 65536.  This will set your stack size to 64 MB.

## During eval the following error is seen: About to throw exception 'cuDNN failure 8: CUDNN_STATUS_EXECUTION_FAILED ; GPU=0 ; hostname=haha; expr=err'

Set the minibatchSize property to a smaller value (e.g. `minibatchSize=2`).
 
## When I compile CNTK using VS2013, I see a compiler error, what is wrong?
You must upgrade Visual Studio *2015*, update 3. See here: [Setting up CNTK on Windows](./Setup-CNTK-on-Windows.md)

## I enabled Image Reader with zip support and get "Plugin not found: 'Cntk.Deserializers.Image-<VERSION>.dll' (old name 'ImageReader.dll')" error when running Image Reader unit tests or trying to use the reader. What might be wrong?
Check that you correctly installed [zlib and libzip](./Setup-CNTK-on-Windows.md#optional-zlib-and-libzip), especially that you have not forgotten to [rename `zlib.dll` to `zlib1.dll`](./Setup-CNTK-on-Windows.md#preparing-the-environment).

## I have just downloaded and installed CNTK **binary package** and want to run a job, but get weird errors, like missing CUDA libraries.
Please, check carefully what you have in your `PATH`. Especially, if it is a *shared* development machine. Often this is cause by older (previous) versions of CNTK or CNTK components that are reachable through the configured PATH.

## On Windows I installed a new version of NVIDIA driver and now CNTK build fails with the errors like ``` ..\Common\BestGpu.cpp(24): fatal error C1083: Cannot open include file: 'nvml.h': No such file or directory```
You have selected *Perform a clean installation* option in NVIDIA Driver Installer. That results in the removal of *GPU Deployment Kit (GDK)*. To repair the system, perform the following steps:
* Launch CUDA Installer
* Select *Custom (Advanced)* Installation
* Unselect all installation options, except *GPU Deployment Kit*
* This will automatically select *Graphics Driver* option - it is expected. Leave it selected
* Proceed with CUDA installation
* After successful CUDA installation launch the installation of the desired Graphics Driver version
* Select *Custom (Advanced)* Installation
* Ensure that *Perform a clean installation* is **NOT** selected and proceed with the installation

## Initial read time takes a very long time **or** I'm getting one of the following exceptions: "OS call failed or operation not supported on this OS" or "EXCEPTION occurred: CUSPARSE failure 1" or a Segfault with AllocationFailureHandler in the stack.

One possible reason here is the excessive memory pressure caused by loading the whole data set in memory with the default (i.e., unlimited) randomization window. Please try running your workload with an explicit `randomizationWindow` value, which will limit the amount of input data cached in memory. To do that, add the following parameters to your `reader` configuration section (using `10000` as an example, you may choose any value that fits in memory and ensures good randomization): 
```
   randomize=true
   randomizationWindow=10000 #(assuming that 10K samples << total available memory)
```

## I get errors when using Eval C# library Cntk.Eval.Wrapper.dll (old name EvalWrapper.dll) in Azure web app like the following: "Could not load file or assembly 'some CNTK dlls', or an exception System.Runtime.InteropServices.SEHException, or "InternalServiceFault: External component has thrown an exception.". 

First please make sure that all CNTK dependency dlls are deployed to the Azure web app.
Then you have to set your Azure web app to use 64-bit VM. In order to allow the Azure web app to load CNTK unmanaged dlls, you should change the PATH variable by adding the following code in the `Application_Start()` method in `global.asax`:
```   
    string pathValue = Environment.GetEnvironmentVariable("PATH");
    string domainBaseDir = AppDomain.CurrentDomain.BaseDirectory;
    string cntkPath = domainBaseDir + @"bin\";
    pathValue += ";" + cntkPath;
    Environment.SetEnvironmentVariable("PATH", pathValue);
```
 
Please see the "[Evaluate a model in an Azure WebApi](./Evaluate-a-model-in-an-Azure-WebApi.md)" page for detailed steps.  

## I can build my application using Cntk.Eval-<VERSION>.lib/dll (old name EvalDll.lib/dll), but got the exception like below when running

	An unhandled exception of type 'Microsoft.MSR.CNTK.Extensibility.Managed.CNTKRuntimeException' occurred in Cntk.Wrapper-<VERSION>.dll
	Additional information: configparameters: required parameter missing: unknown:modelPath
We use [new shared library file names](./CNTK-Shared-Libraries-Naming-Format.md) in the text below.

Possible reasons are:  
* Different build configuration (release or debug) between your application and the `Cntk.Eval-<VERSION>.lib/dll`. If you build your application in release, please link against the release version of `Cntk.Eval-<VERSION>.dll`. Same for the debug configuration. This error  happens usually for C++ application.  
* Different GCC compiler versions on Linux: The `Cntk.Eval` library on Linux is built with GCC 4.8. If you use a different GCC version for your application, you might see the above error
* The modelPath is incorrect.  Currently, the modelPath has to be specified as a full path. In the CSEvalClient example, the relative path is combined with the current working directory to build the full path. And the CreateNetwork() must be passed a string containing the "modelPath=" attribute specifying the model's file path. Currently, mapped drive is not supported for modelpath.

## I get the following result with CNTKTextFormat-Reader -- WARNING: Exhausted all input expected for the current sequence while reading a floating point value at offset

[CTF reader](./BrainScript-CNTKTextFormat-Reader.md) requires that Each well-formed line must end with either a "Line Feed" \n or "Carriage Return, Line Feed" \r\n symbols (including the last line of the file).
