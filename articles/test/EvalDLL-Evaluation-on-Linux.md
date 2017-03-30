The CNTK EvalDLL library on Linux is provided as a C++ library. 

The usage pattern for evaluation is the following: 
   
1. Get an instance of the evaluation engine either using GetEvalF() (for the `float` data type) or GetEvalD() (for the `double` data type).    
2. Load the model (or create the network) in the evaluation engine.    
3. Evaluate some input against the model and obtain the corresponding output.    
4. Dispose the model when done.    

The evaluation library, libeval.so, can be found under cntk\lib in the CNTK binary package. If you build CNTK from source code, the libeval.so is available in the lib folder of the build directory. 

Any program using the evaluation library needs to link libeval.so and libcntkmath.so when compiling the program, for example by specifying "-leval -lcntkmath" as well as appropriate search path for both libraries. Please use [the same compiler version](https://github.com/Microsoft/CNTK/wiki/Setup-CNTK-on-Linux#c-compiler) as that used to build libraries. The [Examples/Evaluation/CPPEvalClient](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CPPEvalClient) in the CNTK source code illustrates the usage pattern in Linux. The [Makefile](https://github.com/Microsoft/CNTK/blob/master/Makefile) contains the target EVAL_SAMPLE_CLIENT showing how to build the example.

For details on the C++ API provided by the libeval.so, please refer to the [C++ EvalDLL API](./Native-EvalDLL-API) page in this wiki.