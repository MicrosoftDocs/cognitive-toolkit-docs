The CNTK EvalDLL library on Windows is provided both as C++ and C# library. A Nuget package is also available at Nuget.org.

## Using the EvalDLL library
The EvalDLL.dll is a Windows dll that enables programmatic model evaluation. The dll can only be used in CPU only mode (no GPU is used).  
The usage pattern for this dll is the following:   
       
1. Link the EvalDLL.lib import library into the application.
2. Include the evaluation header file "Eval.h"    
3. Get an instance of the evaluation engine specific for the model's data type (`float` or `double`).    
4. Load the model (or create the network) in the evaluation engine.    
5. Evaluate some input against the model and obtain the corresponding output.    
6. Dispose the model when done.    

For details on the C++ API provided by EvalDll, please refer to the [C++ EvalDLL API](./Native-EvalDLL-API) page in this wiki.

The [CPPEvalClient](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CPPEvalClient) program located in the folder [Examples/Evaluation/CPPEvalClient](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CPPEvalClient) demonstrates the usage of this evaluation interface. Please see the [Eval Examples](./CNTK-Eval-Examples) page for how to build and run examples.

## Using the EvalDLL C# library 
CNTK provides a managed (.Net) library wrapper named EvalWrapper.dll. This library wraps the native EvalDLL library and exposes a managed interface. This interface provides the same functionality as the native interface, with the addition of some convenience methods.    
Alike its native counterpart, this library can only perform evaluations using the CPU (no GPU used). The library is written in CLI/C++ and thus forms the bridge between .Net (e.g. C#) and the native C++ side.

For details regarding the managed API provided by EvalWrapper.DLL, refer to the [Managed EvalDLL API](./Managed-EvalDLL-API) page.

The usage pattern for the managed wrapper is simple:        

    using Microsoft.MSR.CNTK.Extensibility.Managed;
    ...
    try
    {
        using (var model = new IEvaluateModelManagedF())
        {
            // Load model
            model.CreateNetwork(...);
            model.Evaluate(...);
        }
    }
    catch (CNTKException ex)
    {
    ...
    }
    catch (Exception ex)
    {
    ...
    }

There are several [examples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CSEvalClient) of using a programmatic CNTK model evaluation in C# inside the CSEvalClient project.Please see the [Eval Examples](./CNTK-Eval-Examples) page for how to build and run examples.

## NuGet Package
There is currently a NuGet package at Nuget.org (search for CNTK) that provides both the native and managed versions for Debug and Release for the CNTK evaluation libraries (CPU only using MKL). With the NuGet it is possible to simply add the CNTK Eval NuGet to a .Net or Win32 project and call the APIs.
Please refer to the [NuGet Package](./Eval-NuGet) page for details on how to get started with CNTK and NuGet.

If you do not want to use NuGet Package, you can add EvalWrapper.dll as reference to your project. Please make sure in this case that the path to EvalWrapper.dll and its [dependencies](./EvalDLL-Evaluation-on-Windows#shipping-eval-v1-library-with-your-windows-application) are included in the search path of dlls for your application. 

## Shipping EvalDLLLibrary with your Windows application
If you own application uses CNTK EvalDLL library you need to distribute these DLLs with your application:
* EvalDll.dll
* EvalWrapper.dll
* Math.dll
* libiomp5md.dll
* mkl_cntk_p.dll

All these dlls can be found in the CNTK binary release version, see the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).