## Overview

The CNTK Eval NuGet package is a NuGet package containing the necessary libraries and assemblies to enable .NET and Windows C++ applications to perform CNTK model evaluation. There are 3 Nuget packages:

* **CNTK.CPUOnly**: provides [CNTK C#/.NET Managed Eval Library](./CNTK-Library-Managed-API) and [C++ Library](./Native-CNTK-Library-Eval-Interface) for CPU only machines.
* **CNTK.GPU**: provides [CNTK C#/.NET Managed Eval Library](./CNTK-Library-Managed-API) and [C++ Library](./Native-CNTK-Library-Eval-Interface) for GPU enabled machines.
* **Microsoft.Research.CNTK.CpuEval-mkl**: provides [CNTK EvalDll API](./EvalDLL-Evaluation-Overview). It supports CPU only.

## Installation
The package may be obtained through the NuGet Package Manager inside Visual Studio by searching for "CNTK", or downloaded directly from NuGet:

* [https://www.nuget.org/packages/CNTK.CPUOnly/2.0.0-beta15](https://www.nuget.org/packages/CNTK.CPUOnly/2.0.0-beta15)   
* [https://www.nuget.org/packages/CNTK.GPU/2.0.0-beta15](https://www.nuget.org/packages/CNTK.GPU/2.0.0-beta15)
* [https://www.nuget.org/packages/Microsoft.Research.CNTK.CpuEval-mkl/2.0.0-beta15](https://www.nuget.org/packages/Microsoft.Research.CNTK.CpuEval-mkl/2.0.0-beta15)

The current version is 2.0-beta15. When installing, please select the option "Include Prerelease" in "Manage Nuget Packages".
After selecting the appropriate NuGet package to install, use the version selector on the right to explicitly select package version `2.0.0-beta15`.

The CNTK NuGet packages may be installed on a Visual C++ or .NET project (C#, VB.Net, F#, ...). Once installed the project will contain a reference to the managed dll and the required dependent binary libraries will be copied to the output directory.

For instructions on how to install a NuGet package, please refer to the NuGet documentation at:
[https://docs.nuget.org/consume/installing-nuget](https://docs.nuget.org/consume/installing-nuget)

## Current Release
The current release of CNTK Eval Nuget Packages support the following interfaces
* [CNTK Library Managed Eval Interface](./CNTK-Library-Managed-API)
* [EvalDll C# Interface](./Managed-EvalDLL-API) 
* [EvalDll C++ Interface](./Native-EvalDLL-API)

## Linux
There is a Linux equivalent set of libraries (albeit not available through NuGet) that enables CNTK model evaluations in Linux using C++. Refer to the [CNTK Evaluation Overview](./CNTK-Evaluation-Overview) page for details.
