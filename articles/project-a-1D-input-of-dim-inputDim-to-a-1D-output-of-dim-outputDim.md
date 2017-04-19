In C++ API a rank-1 tensor denotes a column and tensors are stored in column major format (i.e. axis 0 is the faster changing dimension, followed by axis 1 and so on). 

```python
hidden_layers = 2
hidden_layers_dim = 50
input = input_variable((input_dim), np.float32)
times_param = parameter(shape=(input.shape[0], output_dim))
t = times(input, times_param)
```
So to project a 1D input of dim “inputDim” to a 1D output of dim “outputDim”, you need to setup things as follows in C++:

```python
input = InputVariable({ inputDim }, DataType::Float);
timesParam = CNTK::Parameter({ outputDim, input.Shape()[0] });
t = Times(timesParam, input);
```

Note how both the tensor shapes and the order of the operands to the Times operation are reversed compared to the python code. In python, to conform to the generally accepted norm established by numpy, a rank-1 tensor denoted a row vector and data layout is row major (i.e. axis 0 is the slowest changing dimension, followed by axis 1 and so on). We internally do the required shape transformations at the SWIG layer to map the python shapes and ops to the C++ implementation correctly.
