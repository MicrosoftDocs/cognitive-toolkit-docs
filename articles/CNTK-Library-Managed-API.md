---
title:   CNTK Library C# API
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   08/03/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   csharp
---

# CNTK Library C# API

The CNTK Library Managed API is implemented in C#, and can be consumed by C# and other .NET languages. The page [Using the CNTK Library Managed API](./CNTK-Library-Evaluation-on-Windows.md#using-cnet-managed-api) and [Using CNTK with C#](./Using-CNTK-with-CSharp.md) present how to use this API in your application. The following sections describe the classes and methods of the CNTK Library Managed Eval API.

## class CNTKLib
This is a static class contains many operations, initialization, and many miscellaneous methods.

***
```cs
public static Function Negate(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise negate operation with the specified input operand.
Parameters:
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.
 
***
```cs
public static Function Sigmoid(Variable operand, string name = "")
```

Sigmoid operation. It takes a input variable and applies Sigmoid operation to it.

Parameters:
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Tanh(Variable operand, string name)
```

Tanh activation. It takes a input variable and applies Tanh operation to it.

Parameters:
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Asin(Variable operand, string name = "");
```

Create an instance of the CNTK built-in elementwise asin operation with the specified input operand.

Parameters:
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Sin(Variable operand, string name = "");
``` 
Create an instance of the CNTK built-in elementwise sine operation with the specified input operand.
Parameters:
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Acos(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise acos operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Cos(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise cosine operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Cosh(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise cosh operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Sinh(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise sinh operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function ReLU(Variable operand, string name = "")
```
Rectifier linear activation. It takes a input variable and applies rectifier linear activation operation to it.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Exp(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise exp operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Log(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise log operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Square(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise square operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Sqrt(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise square-root operation with the specified input operand.
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Softmax(Variable operand, Axis axis, string name = "")
```
Create an instance of the CNTK built-in softmax operation on specified axis on a specified tensor input operand
Parameters:
* `operand`: The input variable of the operation. It can be the ouput of another function.
* `axis`: The axis to apply softmax operation
* `name`: the name of the operation.

***
```cs
public static Function Hardmax(Variable operand, string name = "");
```
Create an instance of the CNTK built-in hardmax operation on specified tensor input operand
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Transpose(Variable operand, string name = "");
```
Create an instance of the CNTK built-in transpose operation on the specified 1D or 2D input operand
* `operand`: The input variables. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Dropout(Variable operand, dropoutRate, uint seed, string name = "");
```
Create an instance of the dropout operation on specified tensor input operand
* `operand`: The input variables. It can be the ouput of another function.
* `dropoutRate`: 
* `seed`:
* `name`: the name of the operation.

***
```cs
public static Function Reshape(Variable operand, NDShape replacementShape, Axis beginAxis, Axis endAxis, string name = "");
```
Create an instance of the reshape operation on specified tensor input operand

***
```cs
public static Function Times(Variable leftOperand, Variable rightOperand, string name = "");
```
Create an instance of the CNTK built-in tensor multiplication operation with the specified input operands.

***
```cs
public static Function TransposeTimes(Variable leftOperand, Variable rightOperand, uint outputRank, string name = "");
```
Create an instance of the CNTK built-in matrix multiplication operation with the transpose of the left input operand
and the specified right operand. Only accepts left operands of ranks 1 or 2.

***
```cs
public static Function Plus(Variable leftOperand, Variable rightOperand, string name = "")
```
A binary elementwise tensor addition operation. 
Parameters:
* `leftOperand`: The left variable of the plus operation. It can be the ouput of another function.
* `rightOperand`: The left variable of the plus operation. It can be the ouput of another function.
* `name`: the name of the operation.

***
```cs
public static Function Minus(Variable leftOperand, Variable rightOperand, string name = "");
```
Create an instance of the CNTK built-in elementwise tensor subtraction operation with the specified input operands.

***
```cs
public static Function LogAddExp(Variable leftOperand, Variable rightOperand, string name = "");
```
Create an instance of the CNTK built-in elementwise tensor operation that computes the log of the sum of the exponentials of the specified input operands.

***
```cs
public static Function Pow(Variable leftOperand, Variable rightOperand, string name = "");
```
Create an instance of the CNTK built-in elementwise tensor operation that computes the leftOperand raised to the power of the right operand.

***
```cs
public static Function ElementTimes(Variable leftOperand, Variable rightOperand, string name = "");
```
Create an instance of the CNTK built-in elementwise multiplication operation on specified tensor input operands.

***
```cs
public static Function ElementDivide(Variable leftOperand, Variable rightOperand, string name = "");
```
Create an instance of the CNTK built-in elementwise division operation on specified tensor input operands.

***
```cs
public static Function CosineDistance(Variable leftOperand, Variable rightOperand, string name = "");
```
Create an instance of the CNTK built-in operation to compute the cosine distance for the specified input operands.

***
```cs
public static Function CosineDistanceWithNegativeSamples(Variable leftOperand, Variable rightOperand, uint shiftWindow, uint numberOfNegativeSamples, string name = "");
```
Create an instance of the CNTK built-in operation to compute the cosine distance with negative samplesfor the specified input operands.

***
```cs
public static Function BinaryCrossEntropy(Variable prediction, Variable targets, string name = "");
```
Create an instance of the CNTK built-in operation to compute binary cross-entropy for specified input operands.

***
```cs
public static Function WeightedBinaryCrossEntropy(Variable prediction, Variable targets, Variable weights, string name = "");
```
Create an instance of the CNTK built-in operation to compute weighted binary cross-entropy for specified input operands.

***
```cs
public static Function CrossEntropyWithSoftmax(Variable prediction, Variable labels, Axis axis, string name = "");
```
Create an instance of the CNTK built-in operation to compute cross-entropy with softmax for specified input operands.

***
```cs
public static Function ClassificationError(Variable prediction, Variable labels, uint topN, Axis axis, string name = "");
```
Create an instance of the CNTK built-in operation for computing the classification prediction error for specified operands.

***
```cs
public static Function PastValue(Variable operand, Variable initialState, uint offset = 1, string name = "");
```
Create an instance of the CNTK built-in operation for getting the past value along the lone dynamic axis of the specified operand.

***
```cs
public static Function FutureValue(Variable operand, Variable initialState, uint offset = 1, string name = "");
```
Create an instance of the CNTK built-in operation for getting the future value along the lone dynamic axis of the specified operand.

***
```cs
public static Function Convolution(Variable convolutionMap,
                                     Variable operand,
                                     NDShape strides = { 1 },
                                     BoolVector sharing = { true },
                                     BoolVector autoPadding = { true },
                                     NDShape dilation = { 1 },
                                     uint reductionRank = 1,
                                     uint maxTempMemSizeInSamples = 0,
                                     string name = "");
```
Convolution

***
```cs
public static Function ROIPooling(Variable operand,
                                    Variable rois,
                                    PoolingType poolingType,
                                    NDShape roiOutputShape,
                                    double spatialScale,
                                    string name= "");
```
Create an instance of the CNTK built-in ROI pooling operation on specified tensor input operands with the specified output shape

***
```cs
public static Function Pooling(Variable operand,
                                 PoolingType poolingType,
                                 NDShape poolingWindowShape,
                                 NDShape strides = {1},
                                 BoolVector autoPadding = {false},
                                 const bool ceilOutDim = false,
                                 const bool includePad = false,
                                 string name = "");
```

***
```cs
public static Function BatchNormalization(Variable operand,
                                            Variable scale,
                                            Variable bias,
                                            Variable runningMean,
                                            Variable runningInvStd,
                                            Variable runningCount,
                                            bool spatial,
                                            double normalizationTimeConstant = 0,
                                            double blendTimeConstant = 0,
                                            double epsilon = 0.00001,
                                            bool useCuDNNEngine = true,
                                            string name = "");
```

***
```cs
public static Function Splice(VariableVector operands, Axis axis, string name = "");
```
Create an instance of the CNTK built-in splice operation to splice together all the specified tensor operands into a single output tensor

***
```cs
public static Function Combine(VariableVector operands, string name = "");
```
Create a new Function instance which just combines the outputs of the specified list of 'operands' Functions such that the 'Outputs' of the
new 'Function' are union of the 'Outputs' of each of the specified 'operands' Functions.
E.g. When creating a classification model, typically the CrossEntropy loss Function and the ClassificationError Function comprise the two roots
of the computation graph which can be "Combined" to create a single Function with 2 outputs; viz. CrossEntropy loss and ClassificationError output.

***
```cs
public static Function LeakyReLU(Variable operand, string name = "");
```
Create an instance of the CNTK built-in elementwise leaky linear rectifier operation with the specified input operand.

## class Learner


## class Function

A `Function` denotes a symbolic computation with zero or more input arguments and one or more outputs. A Function may be primitive or composite (comprised of other Function instances whose inputs and outputs are wired together). A Function effectively is a computation graph composed of other primitive Functions (denoting computation) as nodes and [`Variable`](#class-variable) objects (denoting data) as the edges and leaves of the graph.

The `Function` class contains the following properties and methods that are relevant to evaluation:

***
```cs
public string Name { get; }
```

Name of the Function.

***
```cs
public IList<Variable> Arguments { get; }
```

List of all input Variables of the Function that are not of type Parameter or Constant. These Variables are required inputs in order to compute the outputs of the Function.

***
```cs
public Variable Output { get; }
```

The single output Variable of the Function, if there is only one. Otherwise a runtime exception is raised on access.

***
```cs
public IList<Variable> Outputs { get; }
```

List of all output Variables of the Function.

***
```cs
public Function RootFunction { get; }
```

Returns the primitive Function at the root of the graph of Functions underlying the Function. If the Function itself is a primitive Function, then (this->RootFunction() == this).

***
```cs
public string OpName { get; }
```

The name of the operation that the Function denotes.

***
```cs
public bool IsComposite { get; }
```

A boolean value indicating whether the Function is a composite Function.

***
```cs
public bool IsPrimitive { get; }
```

A boolean value indicating whether the Function is a primitive Function.

***
```cs
public bool IsBlock { get; }
```

A boolean value indicating whether the Function is a block Function.

***
```cs
public void Evaluate(IDictionary<Variable, Value> inputs, IDictionary<Variable, Value> outputs, DeviceDescriptor computeDevice)
```
```cs
public void Evaluate(IDictionary<Variable, Value> inputs, IDictionary<Variable, Value> outputs, bool createPersistentOutputValues, DeviceDescriptor computeDevice)
```

Evaluates the Function using the specified input. It computes the outputs of the Function based on the [Values](#class-value) provided for each input variable specified in the `inputs` dictionary. The outputs are stored in the output Values corresponding to the output variables in the `outputs` dictionary.

Parameters:
* `inputs`: the input variables and their values of the Function.
* `outputs`: output values for each output variable. The caller may specify the Value object for storing the output or pass `null` in which case the method allocates the storage for the output results. In both cases, on return the Value object contains the result of the corresponding output of the Function. If a `null` Value was specified, and the parameter `createPersistentOutputValues` is not specified or is set to `false`, the Value object returned by the method is temporary and only guaranteed to be valid until the next Forward/Backward call. If it needs to be accessed later, you should specify `createPersistentOutputValues` to `true`, or explicitly `DeepClone` the temporary Value object.
* `computeDevice`: the device on which the computation is executed. It should be aligned with the device on which the model is loaded.
* `createPersistentOutputValues`: Only relevant if a `null` Value was specified in outputs. If it is set to `true`, the method will create persistent Value objects, otherwise the Value objects are temporary and are only valid until the next Forward/Backward call.

***
```cs
public Function Clone(ParameterCloningMethod parameterCloneMethod = ParameterCloningMethod.Share)
```

Clones `this` Function.

Parameters:
* `parameterCloneMethod`: specifies how the parameters of Function are cloned: `ParameterCloningMethod.Share` - Parameters are shared between the Function being cloned and the new clone. This value should be used if the cloned Function is used for concurrent evaluation. `ParameterCloningMethod.Clone` - New learnable Parameters are created and initialized with the current values of the corresponding Parameters of the Function being cloned. `ParameterCloningMethod.Freeze` - Parameters are cloned and made immutable; i.e. Constants in the new clone (e.g. for use as a fixed feature extractor). ParameterCloningMethod is defined [here](#enum-parametercloningmethod).

***
```cs
public Function FindByName(string name, bool nestedSearchInsideBlockFunction)
```

Finds a function with the given name in the Function graph underlying the Function. If more than one function with the same name exists, an exception is thrown. If nestedSearchInsideBlockFunction is true, search all functions inside block functions too.

Parameters:
* `name`: the name to be searched for.
* `nestedSearchInsideBlockFunction`: if it is true, all functions inside block functions are also examined.

***
```cs
public IList<Function> FindAllWithName(string name, bool nestedSearchInsideBlockFunction = false)
```

Finds a list of functions with the given name in the Function graph underlying the Function. If nestedSearchInsideBlockFunction is true, search all functions inside block functions too.

Parameters:
* `name`: the name to be searched for.
* `nestedSearchInsideBlockFunction`: if it is true, all functions inside block functions are also examined.

***
```cs
public static Function Load(string modelFile, DeviceDescriptor computeDevice)
```

Loads a Function from a model file.

Parameters:
* `modelFile`: the path to the model file.
* `computeDevice`: the device on which the Function is loaded.

***
```cs
public static Function Load(byte[] modelBuffer, DeviceDescriptor computeDevice)
```

Loads a Function from a memory buffer representing a model.

Parameters:
* `modelBuffer`: the byte array contains the serialized model content.
* `computeDevice`: the device on which the Function is loaded.

***
```cs
public static Function Combine(IEnumerable<Variable> operands)
```

 Creates a new Function instance which combines the outputs of the specified list of 'operands' of Functions. The 'Outputs' of the new 'Function' are union of the 'Outputs' of each of the specified 'operands' Functions. As an example, when creating a classification model, typically the CrossEntropy loss Function and the ClassificationError Function comprise the roots of the computation graph which can be "Combine"d to create a single Function with 2 outputs; viz. CrossEntropy loss and ClassificationError output.

Parameter:
* `operands`: the list of the functions or variables that should be included in the Outputs of the new function.

***
```cs
public static Function AsComposite(Function rootFunction, string name = "")
```

Creates a composite Function that has the specified rootFunction as its root. The composite denotes a higher-level Function encapsulating the entire graph of Functions underlying the specified rootFunction.

Parameters:
* `rootFunction`: the root of the composite function to be created.
* `name`: the name of the composite function to be created.

***
```cs
public static Function Alias(Variable operand, string name = "")
```

Creates a new Function instance which is just an alias of the specified operand.

Parameters:
* `operand`: the function or variable that the alias function is created for.
* `name`: the name of the new function to be created.

## class Value
A `Value` is a multi-dimensional array with an optional mask and is the actual data fed into or produced from a computation.

***
### The following methods create a Value object using dense input with the specified tensor shape.

***
```cs
public static Value CreateBatch<T>(NDShape sampleShape, IEnumerable<T> batch, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of samples. The number of samples in the batch is the number of elements in `batch` divided by the size of `shape` (A runtime error occurs if the remainder is not zero). The created Value object contains a copy of the specified data in `batch`.

Parameters:
* `sampleShape`: the tensor shape of the Value object.
* `batch`: the data to be contained in the Value object.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value object is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, IEnumerable<T> sequence, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified data in `sequence`. The sequence length is the number of elements in `sequence` divided by the size of `shape` (A runtime error occurs if the remainder is not zero). The created sequence is a new sequence.

Parameters:
* `sampleShape`: the tensor shape of the Value.
* `sequence`: the data to be contained in the Value.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, IEnumerable<T> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified `sequence` data. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence from a previous call to this method. The sequence length is the number of elements in `sequence` divided by the size of `shape` (A runtime error occurs if the remainder is not zero).

Parameters:
* `sampleShape`: the tensor shape of the Value.
* `sequence`: the data to be contained in the Value.
* `sequenceStartFlag`: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in `batchOfSequences`. The number of sequences in the batch is the size of `batchOfSequences`. The length of each sequence is the number of elements in the corresponding sequence of `batchOfSequences` divided by the size of `shape` (A runtime error occurs if the remainder is not zero). Each sequence in `batchOfSequences` is a new sequence.

Parameters:
* `sampleShape`: the tensor shape of the Value.
* `batchOfSequences`: the data to be stored in the Value. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
* `device`: on which device the Value should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in `batchOfSequences`. The number of sequences in the batch is the size of `batchOfSequences`. The length of each sequence is the number of elements in the corresponding sequence of `batchOfSequences` divided by the size of `shape` (A runtime error occurs if the remainder is not zero).

Parameters:
* `sampleShape`: the tensor shape of the Value.
* `batchOfSequences`: the data to be stored in the Value. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
* `sequenceStartFlags`: A collection of boolean values. Each element represents whether the corresponding sequence in `batchOfSequences` is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
* `device`: on which device the Value should be created.
* `readOnly`: the Value is read-only if this flag is `true`.


***
### The following methods create a Value object using one-hot vector input.

***
```cs
public static Value CreateBatch<T>(int dimension, IEnumerable<int> batch, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of `dimension` elements. The number of samples in the batch is the number of elements in `batch`.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `dimension`: the size of dimension of the one-hot vector.
* `batch`: the collection of indexes representing the batch of samples.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of `dimension` elements. The sequence length is the number of elements in `sequence`. The created sequence is a new sequence.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `dimension`: the size of dimension of the one-hot vector.
* `sequence`: the collection of indexes representing the sequence of samples.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of `dimension` elements. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence from a previous call to this method. The sequence length is the number of elements in `sequence`.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `dimension`: the size of dimension of the one-hot vector.
* `sequence`: the collection of indexes representing the sequence of samples.
* `sequenceStartFlag`: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of `dimension` elements. The number of sequences is the number of elements in the outer collection of `batchOfSequences`. The length of each sequence is the number of elements of the corresponding sequence in the collection list of `batchOfSequences`. Each sequence in `batchOfSequences` is a new sequence.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `dimension`: the size of dimension of the one-hot vector.
* `batchOfSequences`: the collection of indexes representing sequences of samples. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of `dimension` elements. The number of sequences is the number of elements in the outer collection of `batchOfSequences`. The length of each sequence is the number of elements of the corresponding sequence in the inner collection of `batchOfSequences`.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `dimension`: the size of dimension of the one-hot vector.
* `batchOfSequences`: the collection of indexes representing sequences of samples. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
* `sequenceStartFlags`: A collection of boolean values. Each element represents whether the corresponding sequence in `batchOfSequences` is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
### The following methods create a Value object using sparse input.
Currently the Compressed Sparse Column Format (CSC) is supported. The CSC format stores the matrix in column-major format, and the array containing the column indices is compressed. A matrix in CSC format is represented by the following parameters:
* `nonZeroValues`: the data array that holds all nonzero values of the matrix in column-major format.
* `rowIndices`: the array that contains the row indices of the corresponding elements in array `nonZeroValues`.
* `colStarts`: the array that holds indices into the arrays `rowIndices` and `nonZeroValues`.

A detailed description of the CSC format can be found [here](http://docs.nvidia.com/cuda/cusparse/index.html#compressed-sparse-column-format-csc).

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The created sequence is a new sequence.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `sampleShape`: the tensor shape of the Value. For sparse input, the tensor shape leading dimensionality must be the same as the total size of the tensor shape.
* `sequenceLength`: the sequence length, which is also the number of rows in the sparse matrix.
* `colStarts`: the array holds indices for each column into the arrays `rowIndices` and `nonZeroValues`.
* `rowIndices`: the array that contains the row indices of the corresponding elements in array `nonZeroValues`.
* `nonZeroValues`: the array that holds all nonzero values in the sparse matrix.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence from a previous call to this method.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `sampleShape`: the tensor shape of the Value. For sparse input, the tensor shape leading dimensionality must be the same as the total size of the tensor shape.
* `sequenceLength`: the sequence length, which is also the number of rows in the sparse matrix.
* `colStarts`: the array holds indices for each column into the arrays `rowIndices` and `nonZeroValues`.
* `rowIndices`: the array that contains the row indices of the corresponding elements in array `nonZeroValues`.
* `nonZeroValues`: the array that holds all nonzero values in the sparse matrix.
* `sequenceStartFlag`: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(int dimension, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The created sequence is a new sequence.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `dimension`: the number of columns in the sparse matrix.
* `sequenceLength`: the sequence length, which is also the number of rows in the sparse matrix.
* `colStarts`: the array holds indices for each column into the arrays `rowIndices` and `nonZeroValues`.
* `rowIndices`: the array that contains the row indices of the corresponding elements in array `nonZeroValues`.
* `nonZeroValues`: the array that holds all nonzero values in the sparse matrix.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(int dimension, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence.

Parameters:
* `T`: data type of the created Value object. Currently, `float` and `double` are supported.
* `dimension`: the number of columns in the sparse matrix.
* `sequenceLength`: the sequence length, which is also the number of rows in the sparse matrix.
* `colStarts`: the array holds indices for each column into the arrays `rowIndices` and `nonZeroValues`.
* `rowIndices`: the array that contains the row indices of the corresponding elements in array `nonZeroValues`.
* `nonZeroValues`: the array that holds all nonzero values in the sparse matrix.
* `sequenceStartFlag`: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
### The following methods create a Value object from NDArrayView.

***
```cs
public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object based on a collection of NDArrayViews. Each sequence in `sequences` is a new sequence.

Parameters:
* `sampleShape`: the tensor shape of the Value being created.
* `sequences`: a collection of sequences represented by NDArrayView. Each NDArrayView represents a sequence.
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
```cs
public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object based on a collection of NDArrayViews. The sequenceStartFlags specifies whether a sequence is a new sequence or continuation of a previous sequence.

Parameters:
* `sampleShape`: the tensor shape of the Value being created.
* `sequences`: a collection of sequences represented by NDArrayView. Each NDArrayView represents a sequence.
* `sequenceStartFlags`: A collection of boolean values. Each element represents whether the corresponding sequence in `sequences` is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
* `device`: on which device the Value object should be created.
* `readOnly`: the Value is read-only if this flag is `true`.

***
### A Value object contains the following properties and methods.

***
```cs
public Value(NDArrayView data)
```

Constructs a multi-dimensional value with no mask.

***
```cs
public Value(NDArrayView data, NDMask mask)
```

Constructs a multi-dimensional value with an associated mask.

***
```cs
public DataType DataType { get; }
```

[DataType](#enum-datatype) of the data contained in the Value object.

***
```cs
public DeviceDescriptor Device { get; }
```

The descriptor of the device that the Value resides on.

***
```cs
public NDShape Shape { get; }
```

The shape of the Value.

***
```cs
public StroageFormat StorageFormat { get; }
```

The storage format of the Value.

***
```cs
public bool IsSparse { get; }
```

A boolean value indicating whether the Value contains data in sparse storage format.

***
```cs
public bool IsReadOnly { get; }
```

A boolean value indicating whether the Value is read-only.

***
```cs
public int maskedCount { get; }
```

The number of masked/invalid values in the Value.

***
```cs
public void CopyFrom(Value Source)
```

Copies the contents of `source` to the Value. The shapes of the `Source`'s data and mask must be identical to `this` Value's data and mask.

Parameter:
* `Source`: The source object from which the Value is copied.

***
```cs
public Value DeepClone()
```

Creates a new Value with newly allocated storage on the same device as `this` Value and copies `this` Value's contents into the newly allocated Value.

***
```cs
public Value DeepClone(bool readOnly)
```

Creates a new Value with newly allocated storage on the same device as `this` Value and copies `this` Value's contents into the newly allocated Value.

Parameter:
* `readOnly`: the new Value object is read-only if this flag is `true`.

***
```cs
public Value Alias(bool readOnly)
```

Creates a new Value which is an alias of `this` Value.
Parameter:
* `readOnly`: the new Value object is read-only if this flag is `true`.

***
```cs
public IList<IList<T>> GetDenseData<T>(Variable outputVariable)
```

Gets the data stored in the Value object as a list of sequences with variable length in dense format. This method returns an IList<IList<T>>. Each element of the outer list represents a sequence. Each sequence, represented by IList<T>, contains a variable number of samples. Each sample consists of a fixed number of elements with type of 'T'. The number of elements is determined by the shape of outputVariable. The number of samples is the count of elements in IList<T> divided by the count of elements of the sample.

Parameter:
* `outputVariable`: The variable that the Value denotes to. The shape of the variable should match the shape of the Value.

***
```cs
public IList<IList<int>> GetOneHotData(Variable outputVariable)
```

Gets the data stored in the Value object as a list of sequences with variable length in one-hot vector format. This method returns an IList<IList<T>>. Each element of the outer list represents a sequence. Each sequence, represented by IList<int>, contains a variable number of samples. Each sample is represented by an index to the one-hot vector. The number of samples is the count of elements in IList<int>.

Parameter:
* `outputVariable`: The variable that the Value denotes to. The size of the one-hot vector should match that defined in the variable.

***
```cs
public void GetSparseData<T>(Variable outputVariable, out int sequenceLength, out IList<int> colStarts, out IList<int> rowIndices, out IList<T> nonZeroValues, out int numNonZeroValues)
```

Copy the data stored in the Value object to the buffers representing a sequence in CSC sparse format. The sequence buffer will be resized if necessary. The Value should have the same tensor shape as outputVariable. On return, `sequenceLength` is set to the length of the sequence stored in the Value, and `colStarts`, `rowIndices` and `nonZeroValues` contain the data of column start indexes, row indexes and non-zero values, and `numNonZeroValues` is set to number of non-zero values contained in `this` Value.

Parameters:
* `outputVariable`: denotes the shape and dynamic axes when copying data from this Value to the buffers.
* `sequenceLength`: on return, it is set to the length of the sequence stored in the Value.
* `colStarts`: on return, it contains indices into the `nonZeorValues` of the first non-zero element of each column of the matrix.
* `rowIndices`: on return, it contains the row indexes of each non-zero element of the matrix.
* `nonZeroValues`: on return, it contains values of all non-zero elements of the matrix.
* `numNonZeroValues`: on return, it returns the number of non-zero elements of the matrix.

***
### The following methods will be deprecated soon. Please use GetDenseData() and GetOneHotData() described above.

***
```cs
public void CopyVariableValueTo<T>(Variable outputVariable, List<List<T>> sequences)
```

Copies the data stored in the Value into the buffer provided by `sequences`. The `sequences` is a list of sequences with variable length. The number of items contained in the outer list of `sequences` is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `List<T>`, contains a variable number of samples. Each sample consists of a fixed number of elements with type of `T`. The number of elements of a sample is determined by the shape of `outputVariable`. The shape of the variable should match the shape of the Value.

Parameters:
* `outputVariable`: denotes the shape and dynamic axes when copying data from the Value to the `sequences`.
* `sequences`: the output buffer used to store the data copied from the Value.

***
```cs
public void CopyVariableValueTo(Variable outputVariable, List<List<uint>> sequences
```

Copies the data stored in the Value object into the buffer provided by `sequences`. The `sequences` is a list of sequences with variable length. The number of items contained in the outer list of `sequences` is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `List<uint>`, contains a variable number of samples. Each sample is represented by an index pointing to the non-zero value in the one-hot vector. The dimension size of the one-hot vector should match that defined in the `outputVariable`.

Parameters:
* `outputVariable`: denotes the shape and dynamic axes when copying data from the Value to the `sequences`.
* `sequences`: the output buffer used to store the data copied from the Value.

## class Variable
Denotes a symbolic entity corresponding to the inputs and outputs of a [`Function`](#class-function).

The properties of `Variable` that are often used in evaluation include:

***
```cs
public string Name { get; }
```

Name of the Variable.

***
```cs
public NDShape Shape { get; }
```

Shape of the Variable.

***
```cs
public DataType DataTye { get; }
```

DataType(#enum-datatype) of the data that the Variable represents.

***
```cs
public VariableKind Kind { get; }
```

The [VariableKind](#enum-variablekind) of the Variable.

***
```cs
public bool IsSparse { get; }
```

A boolean value indicating whether the Variable denotes sparse data.

***
```cs
public bool IsInput { get; }
```

A boolean value indicating whether the Variable is an Input.

***
```cs
public bool IsOutput { get; }
```

A boolean value indicating whether the Variable is an Output.

***
```cs
public bool IsParameter { get; }
```

A boolean value indicating whether the Variable is a Parameter.

***
```cs
public bool IsConstant { get; }
```

A boolean value indicating whether the Variable is a Constant.

***
```cs
public bool IsPlaceholder { get; }
```

A boolean value indicating whether the Variable is a Placeholder.

***
```cs
public IList<Axis> DynamicAxes { get; }
```

Returns the dynamic axes of the Variable.

***
```cs
public Function Owner { get; }
```

Returns the Function which the Variable is an output of. Returns `null` when the Variable is not of VariableKind Output.

## class DeviceDescriptor
Denotes a computation device instance.

The class `DeviceDescriptor` contains the following properties and methods:

***
```cs
public int Id { get; }
```

The Id of the device.

***
```cs
public DeviceKind Type { get; }
```

[DeviceKind](#enum-devicekind) of the device.

***
```cs
public static DeviceDescriptor CPUDevice { get; }
```

The DeviceDescriptor representing the CPU device on the local system.

***
```cs
public static DeviceDescriptor GPUDevice(int deviceId)
```

Gets the DeviceDescriptor of the GPU device on the local system with the specified CUDA device ID.

Parameter:
* `deviceId`: the CUDA device ID.

***
```cs
public static IList<DeviceDescriptor> AllDevices()
```

Gets a list of descriptors of all available/supported devices.

## class NDShape
Denotes a multi-dimensional rectangular shape.

***
```cs
public NDShape()
```

Constructs a NDShape with 0 axes, which denotes a scalar.

***
```cs
public NDShape(int numAxes, int dimension)
```

Constructs a NDShape instance with the specified rank and dimension size. Each axis has the same dimensionality.

Parameters:
* `numAxes`: the number of axes of the shape.
* `dimension`: the dimension size, applied to each axis.

***
```cs
public NDShape(int numAxes)
```

Constructs a NDShape instance with the specified rank. The dimensionality in each axis is `NDShape.InferredDimension`.

Parameter:
* `numAxes`: the number of axes of the shape.

***
```cs
public static NDShape CreateNDShape(IEnumerable<int> dimensions)
```

Creates a NDShape instance with specified dimensions.
Parameter:
* `dimensions`: the dimension size of each axis.

***
```cs
public int Rank { get; }
```

The rank of the NDShape.

***
```cs
public IList<int> Dimensions { get; }
```

The dimensions of the NDShape.

***
```cs
public int TotalSize { get; }
```

The total size of the rectangular shape that the Shape denotes.

***
```cs
public int this[int key] { get; }
```

Returns the dimension size of the specified axis.

***
```cs
public int IsUnknown { get; }
```

A boolean indicating if the shape is the special Unknown shape.

***
```cs
public int HasInferredDimension { get; }
```

A boolean value indicating if the dimension size for any of the axes of the shape is unknown/inferred (NDShape.InferredDimension).

***
```cs
public NDShape AppendShape(NDShape shape)
```

Creates and returns a new shape constructed by appending the dimensions of the specified 'shape' to `this` shape's dimensions.

***
```cs
public NDShape SubShape(int beginAxisId, int endAxisId)
```

Creates and returns a new NDShape instance with the same dimensions as `this` shape's specified axis range [beginAxisId, endAxisId).

***
```cs
public NDShape SubShape(int beginAxisId)
```

Creates and returns a new NDShape instance with the same dimensions as `this` shape's axis range between the beginAxisId axis (inclusive) and the last axis (inclusive).

## class NDArrayView
Denotes a multi-dimensional writable or read-only array of elemental values. This type denotes a view and there may be multiple simultaneous views of the data underlying a NDArrayView instance. The underlying data is stored in sparse or dense format, and is located on a specific device.

***
```cs
public NDArrayView(NDShape viewShape, float[] dataBuffer, DeviceDescriptor device, bool readOnly = false)
```
```cs
public NDArrayView(NDShape viewShape, double[] dataBuffer, DeviceDescriptor device, bool readOnly = false)
```

Constructs a NDArrayView with the specified 'dataBuffer' in dense format as the backing storage on the specified device.

Parameters:
* `viewShape`: the shape of the NDArrayView being created.
* `dataBuffer`: the data values contained in the NDArrayView. The 'dataBuffer' must be at least as large as the total size of the specified 'viewShape' and must outlive the created NDArrayView object.
* `device`: on which device the NDArrayView object should be created.
* `readOnly`: the NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, float[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
```
```cs
public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, double[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
```

Constructs a NDArrayView with the specified storage in sparse CSC format on the specified device. The specified sparse data must outlive the created NDArrayView object.

Parameters:
* `viewShape`: the shape of the NDArrayView being created.
* `colStarts`: the array holds indices for each column into the arrays `rowIndices` and `nonZeroValues`.
* `rowIndices`: the array that contains the row indices of the corresponding elements in array `nonZeroValues`.
* `nonZeroValues`: the array that holds all nonzero values in the sparse matrix. The specified sparse data must outlive the created NDArrayView object.
* `device`: on which device the NDArrayView object should be created.
* `readOnly`: the NDArrayView object is read-only if this flag is `true`.

***
```cs
public DeviceDescriptor Device
```

The DeviceDescriptor of the device that the NDArrayView resides on.

***
```cs
public NDShape Shape
```

The shape of the NDArrayView.

***
```cs
public DataType DataType
```

The DataType of the data that the NDArrayView stores.

***
```cs
public StroageFormat StorageFormat
```

The storage format of the NDArrayView.

***
```cs
public bool IsSparse
```

A boolean value indicating whether the NDArrayView contains data in sparse storage format.

***
```cs
public bool IsReadOnly`

A boolean value indicating whether the NDArrayView is read-only.

***
```cs
public NDArrayView DeepClone()
```

Creates a new NDArrayView with newly allocated storage on the same device as `this` view and copies `this` view's contents into the newly allocated view.

***
```cs
public NDArrayView DeepClone(bool readOnly)
```

Creates a new NDArrayView with newly allocated storage on the same device as `this` view and copies `this` view's contents into the newly allocated view.

Parameter:
* `readOnly`: the new NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView DeepClone(DeviceDescriptor device, bool readOnly)
```

Creates a new NDArrayView with newly allocated storage on the specified device and copies `this` view's contents into the newly allocated view.

Parameters:
* `device`: on which device the new NDArrayView object should be created.
* `readOnly`: the new NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView Alias(bool readOnly)
```

Creates a new NDArrayView which is an alias of `this` view; i.e. a new view of the same shape as `this` over the same underlying data.

Parameter:
* `readOnly`: the new NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView AsShape(NDShape newShape)
```

Creates a new NDArrayView which is an alias of `this` view but with a new shape.

Parameter:
* `newShape`: the shape of the new NDArrayView object.

***
```cs
public void CopyFrom(NDArrayView source)
```

Copies the contents of the 'source' NDArrayView to `this` view. The shapes of the 'source' view and `this` view must be identical.

Parameter:
* `source`: the source NDArrayView whose content is copied to `this` view.

***
```cs
public void ChangeDevice(DeviceDescriptor device)
```

Changes the device of the NDArrayView to the specified device.

Parameter:
* `device`: the target device of the NDArrayView object.

## class NDMask
Denotes a multi-dimensional mask used for specifying specific sections of a NDArrayView object as masked/invalid. This type denotes a view and there may be multiple simultaneous views of the data underlying a NDMask instance.

***
```cs
public NDMask(NDShape shape, DeviceDescriptor device)
```

Construct a new Mask object of specified shape on the specified device.

Parameters:
* `shape`: the shape of the NDMask object.
* `device`: the target device of the MDMask object.

***
```cs
public int MaskedCount { get; }
```

The number of masked/invalid values.

***
```cs
public int Device { get; }
```

The descriptor of the device that the mask resides on

***
```cs
public int Shape { get; }
```

The shape of the mask.

***
```cs
public void InvalidateSection(IEnumerable<int> sectionOffset, NDShape sectionShape)
```

Mask the specified sub-section as invalid.

Parameters:
* `sectionOffset`: A list specifying start positions of the section that should be masked as invalid.
* `sectionShape`: The shape describes the section that should be masked as invalid.

***
```cs
public void MarkSequenceBegin(IEnumerable<int> offset)
```
Mark the specified position as sequence begin.

Parameter:
* `sectionOffset`: A list specifying start positions in the mask that should be masked as sequence begin.

***
```cs
public void MarkSequenceBegin(IEnumerable<int> offset, NDShape sectionShape)
```
Mark the specified sub-section as sequence begin.

Parameters:
* `offset`: A list specifying start positions of the section that should be masked as sequence begin.
* `sectionShape`: The shape describes the section that should be masked as sequence begin.

***
```cs
public void Clear()
```
Clear the mask. All currently values masked as invalid are set to valid.

***
```cs
public NDMask DeepClone(DeviceDescriptor device)
```

Creates a new NDMask with newly allocated storage on the specified device and copies `this` mask's contents into the newly allocated view.

Parameter:
* `device`: the target device of the MDMask object.

***
```cs
public NDMask DeepClone()
```

Creates a new NDMask with newly allocated storage on the same device as `this` mask and copies `this` mask's contents into the newly allocated mask.

***
```cs
public NDMask Alias()
```

Creates a new NDMask which is an alias of `this` mask.

***
```cs
public void CopyFrom(NDMask source)
```

Copies the contents of the `source` NDMask to `this` mask. The shapes of the `source` mask and `this` mask must be identical.


Parameter:
* `source`: the source NDMask whose content is copied to `this` view.

## class Axis
Denotes an Axis of a [`Variable`](#class-variable). Besides the static axes corresponding to each of the axes of the Variable's shape, Variables of kind 'Input' and any 'Output' Variables dependent on an 'Input' Variable also have 2 additional dynamic axes whose dimensions are known only when the Variable is bound to actual data during compute (viz. sequence axis and batch axis denoting the axis along which multiple sequences are batched)

The following properties are defined in the class `Axis`

***
```cs
public string Name { get; }
```

Name of the Axis.

***
```cs
public bool IsStatic { get; }
```

Returns a boolean value indicating whether the Axis corresponds to a static axis.

***
```cs
public bool IsDynamic { get; }
```

Returns a boolean value indicating whether the Axis corresponds to a dynamic axis.

## class Utils

***
```cs
public static void SetMaxNumCPUThreads(int numCPUThreads)
```

Sets the process-wide maximum number of CPU threads to be used by any individual compute operation.

***
```cs
public static int GetMaxNumCPUThreads()
```

Returns the process-wide maximum number of CPU threads to be used by any individual compute operation.

***
```cs
public static void SetTraceLevel(TraceLevel value)
```

Specifies global logging verbosity level.

***
```cs
public static TraceLevel GetTraceLevel()
```

Returns current logging verbosity level.

## enum VariableKind
Denotes the kind of a symbolic [`Variable`](#class-variable) object

```cs
enum VariableKind { Input, Output, Parameter, Constant, Placeholder };
```

## enum DataType
Denotes data type of symbolic data entities or actual data.

```cs
enum DataType { Unknown, Float, Double };
```

## enum DeviceKind
Denotes the type of a computation device.

```cs
enum DeviceKind { CPU, GPU };
```

## enum StorageFormat
Denotes the format of storage underlying an instance of a NDArrayView.

```cs
enum StorageFormat { Dense, SparseCSC, SparseBlockCol };
```

## enum ParameterCloningMethod
Denotes how Parameters are handled when cloning a Function.

```cs
enum ParameterCloningMethod {
    Share, // Parameters are shared between the Function being cloned and the new clone.
    Clone, // New learnable Parameters are created and initialized with the current values of the corresponding Parameters of the Function being cloned.
    Freeze // Parameters are cloned and made immutable.
};
```

## enum TraceLevel
Denotes logging verbosity levels.

```cs
enum TraceLevel { Error, Warning, Info };
```


