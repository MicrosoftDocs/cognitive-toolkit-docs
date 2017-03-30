The CNTK Library on Linux is provided as C++ and Python library. 

## Using C++
The usage pattern on Linux is the same as that on Windows.  
   
The evaluation library, libcntklibrary-2.0.so, can be found under cntk\lib in the CNTK binary package. If you build CNTK from source code, the libcntklibrary-2.0.so is available in the lib folder of the build directory. 

Any program using the evaluation library needs to link libcntklibrary-2.0.so and libcntkmath.so when compiling the program, for example by specifying "-lcntklibrary-2.0 -lcntkmath" as well as appropriate search path for both libraries. Please use [the same compiler version](https://github.com/Microsoft/CNTK/wiki/Setup-CNTK-on-Linux#c-compiler) as that used to build libraries. The [Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) in the CNTK source code illustrates the usage pattern in Linux. The [Makefile](https://github.com/Microsoft/CNTK/blob/master/Makefile) contains the target CNTKLIBRARY_CPP_EVAL_EXAMPLES showing how to build the example.

For details on the CNTK Library for evaluation, please refer to the [CNTK Library Evaluation Interface](./Native-CNTK-Library-Eval-Interface) page in this wiki.

### Using Python
You can use Python to evaluate a pre-trained model. Examples can be found [here](./Evaluate-a-saved-convolutional-network).