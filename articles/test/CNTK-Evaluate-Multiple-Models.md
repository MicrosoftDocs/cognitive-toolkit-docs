##Overview
The CNTK evaluation library (EvalDLL and its .Net wrapper EvalWrapper in Windows and libeval.so in Linux) enables programmatic evaluation of a CNTK model, using a single thread for evaluation. In other words, multiple-concurrent evaluations of a single model instance are not supported.
However, it is possible to load multiple instances of a model and evaluate each model with a single thread. This enables multiple models to be evaluated in parallel, yet each model with a single thread.

##Example
Refer to the `EvaluateMultipleModels` method in the [CSEvalClient](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CSEvalClient) program for an example of a possible implementation for evaluating multiple models concurrently.

