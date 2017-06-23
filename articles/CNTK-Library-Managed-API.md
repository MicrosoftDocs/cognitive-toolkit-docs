---
title:   CNTK Library Eval C# API
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/22/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   csharp
---

# CNTK Library Eval C# API

The CNTK Library Managed Eval API is implemented in C#, and can be consumed by C# and other .NET languages. The page [Using the CNTK Library Managed API](./CNTK-Library-Evaluation-on-Windows.md#using-cnet-managed-api) presents how to use this API in your application. The following sections describe the classes and methods of the CNTK Library Managed Eval API.

## class Function

A `Function` denotes a symbolic computation with zero or more input arguments and one or more outputs. A Function may be primitive or composite (comprised of other Function instances whose inputs and outputs are wired together). A Function effectively is a computation graph composed of other primitive Functions (denoting computation) as nodes and [`Variable`](#class-variable) objects (denoting data) as the edges and leaves of the graph.

The `Function` class contains the following properties and methods that are relevant to evaluation:

***
```cs
public string Name { get; }
```

Name of 'this' Function.

***
```cs
public IList<Variable> Arguments { get; }
```

List of all input Variables of 'this' Function that are not of type Parameter or Constant. These Variables are required inputs in order to compute the outputs of the Function. 

***
```cs
public Variable Output { get; }
```

The single output Variable of 'this' Function, if there is only one. Otherwise a runtime exception is raised on access. 

***
```cs 
public IList<Variable> Outputs { get; }
```

List of all output Variables of 'this' Function.

***
```cs
public Function RootFunction { get; }
```

Returns the primitive Function at the root of the graph of Functions underlying 'this' Function. If 'this' Function itself is a primitive Function, then (this->RootFunction() == this).

***
```cs
public string OpName { get; }
```

The name of the operation that 'this' Function denotes.

***
```cs
public bool IsComposite { get; }
```

A boolean value indicating whether 'this' Function is a composite Function.

***
```cs
public bool IsPrimitive { get; }
```

A boolean value indicating whether 'this' Function is a primitive Function.

***
```cs
public bool IsBlock { get; }
```

A boolean value indicating whether 'this' Function is a block Function.

***
```cs
public void Evaluate(IDictionary<Variable, Value> inputs, IDictionary<Variable, Value> outputs, DeviceDescriptor computeDevice)
```

Evaluates the Function using the specified _arguments_ as input. It computes the outputs of the Function based on the [Values](#class-value) provided for each input variable specified in the _inputs_ dictionary. The outputs are stored in the output Values corresponding to the output variables in the _outputs_ dictionary.

Parameters:
*  _inputs_: the input variables and their values of the Function.
*  _outputs_: output values for each output variable. The caller may specify the Value object for storing the output or pass _null_ in which case the function allocates the storage for the output results. In both cases, on return the Value object contains the result of the corresponding output of the Function. If a _null_ Value was specified, the Value object returned by the method is temporary and only guaranteed to be valid until the next Forward/Backward call. If it needs to be accessed later, you should use the Evaluate() method below which allows you to create persistent Value objects, or you must explicitly _DeepClone_ the temporary Value object.
*  _computeDevice_: the device on which the computation is executed. It should be aligned with the device on which the model is loaded.

***
```cs
public void Evaluate(IDictionary<Variable, Value> inputs, IDictionary<Variable, Value> outputs, bool createPersistentOutputValues, DeviceDescriptor computeDevice)
```

Evaluates the Function using the specified _arguments_ as input. It computes the outputs of the Function based on the [Values](#class-value) provided for each input variable specified in the _inputs_ dictionary. The outputs are stored in the output Values corresponding to the output variables in the _outputs_ dictionary.

Parameters:
*  _inputs_: the input variables and their values of the Function.
*  _outputs_: output values for each output variable. The caller may specify the Value object for storing the output or pass _null_ in which case the method allocates the storage for the output results. In both cases, on return the Value object contains the result of the corresponding output of the Function. If a _null_ Value was specified, and _createPersistentOutputValues_ is false, the Value object returned by the method is temporary and only guaranteed to be valid until the next Forward/Backward call.
* _createPersistentOutputValues_: Only relevant if a _null_ Value was specified in outputs. If it is set to _true_, the method will create persistent Value objects, otherwise the Value objects are temporary and are only valid until the next Forward/Backward call.
*  _computeDevice_: the device on which the computation is executed. It should be aligned with the device on which the model is loaded.

***
```cs
public Function Clone(ParameterCloningMethod parameterCloneMethod = ParameterCloningMethod.Share)
```

Clones 'this' Function.

Parameters:
*  _parameterCloneMethod_: specifies how the parameters of Function are cloned: `ParameterCloningMethod.Share` - Parameters are shared between the Function being cloned and the new clone. This value should be used if the cloned Function is used for concurrent evaluation. `ParameterCloningMethod.Clone` - New learnable Parameters are created and initialized with the current values of the corresponding Parameters of the Function being cloned. `ParameterCloningMethod.Freeze` - Parameters are cloned and made immutable; i.e. Constants in the new clone (e.g. for use as a fixed feature extractor). ParameterCloningMethod is defined [here](#enum-parametercloningmethod).

***
```cs
public Function FindByName(string name, bool nestedSearchInsideBlockFunction)
```

Finds a function with the given name in the Function graph underlying 'this' Function. If more than one function with the same name exists, an exception is thrown. If nestedSearchInsideBlockFunction is true, search all functions inside block functions too.

Parameters:
*  _name_: the name to be searched for.
*  _nestedSearchInsideBlockFunction_: if it is true, all functions inside block functions are also examined.

***
```cs
public IList<Function> FindAllWithName(string name, bool nestedSearchInsideBlockFunction = false)
```

Finds a list of functions with the given name in the Function graph underlying 'this' Function. If nestedSearchInsideBlockFunction is true, search all functions inside block functions too.

Parameters:
*  _name_: the name to be searched for.
*  _nestedSearchInsideBlockFunction_: if it is true, all functions inside block functions are also examined.

***
```cs
public static Function Load(string modelFile, DeviceDescriptor computeDevice)
```

Loads a Function from a model file.

Parameters:
*  _modelFile_: the path to the model file.
*  _computeDevice_: the device on which the Function is loaded.

***
```cs
public static Function Load(byte[] modelBuffer, DeviceDescriptor computeDevice)
```

Loads a Function from a memory buffer representing a model.

Parameters:
*  _modelBuffer_: the byte array contains the serialized model content.
*  _computeDevice_: the device on which the Function is loaded.

***
```cs
public static Function Combine(IEnumerable<Variable> operands)
```

 Creates a new Function instance which combines the outputs of the specified list of 'operands' of Functions. The 'Outputs' of the new 'Function' are union of the 'Outputs' of each of the specified 'operands' Functions. As an example, when creating a classification model, typically the CrossEntropy loss Function and the ClassificationError Function comprise the roots of the computation graph which can be "Combine"d to create a single Function with 2 outputs; viz. CrossEntropy loss and ClassificationError output.

Parameters:
*  _operands_: the list of the functions or variables that should be included in the Outputs of the new function.

***
```cs
public static Function AsComposite(Function rootFunction, string name = "")
```

Creates a composite Function that has the specified rootFunction as its root. The composite denotes a higher-level Function encapsulating the entire graph of Functions underlying the specified rootFunction.

Parameters:   
*  _rootFunction_: the root of the composite function to be created.
*  _name_: the name of the composite function to be created.

***
```cs
public static Function Alias(Variable operand, string name = "")
```

Creates a new Function instance which is just an alias of the specified operand.

Parameters:
*  _operand_: the function or variable that the alias function is created for.
*  _name_: the name of the new function to be created.

## class Value
A `Value` is a multi-dimensional array with an optional mask and is the actual data fed into or produced from a computation.

***
### The following methods create a Value object using dense input with the specified tensor shape.

***
```cs
public static Value CreateBatch<T>(NDShape sampleShape, IEnumerable<T> batch, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of samples. The number of samples in the batch is the number of elements in _batch_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero). The created Value object contains a copy of the specified data in _batch_.
 
Parameters:
*  _sampleShape_: the tensor shape of the Value object. 
*  _batch_: the data to be contained in the Value object. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value object is read-only if this flag is `true`. 

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, IEnumerable<T> sequence, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified data in _sequence_. The sequence length is the number of elements in _sequence_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero). The created sequence is a new sequence.
 
Parameters:
*  _sampleShape_: the tensor shape of the Value. 
*  _sequence_: the data to be contained in the Value. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, IEnumerable<T> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified _sequence_ data. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence from a previous call to this method. The sequence length is the number of elements in _sequence_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero).

Parameters:
*  _sampleShape_: the tensor shape of the Value. 
*  _sequence_: the data to be contained in the Value.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
```cs
public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero). Each sequence in _batchOfSequences_ is a new sequence.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero).

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.


***
### The following methods create a Value object using one-hot vector input.

***
```cs
public static Value CreateBatch<T>(int dimension, IEnumerable<int> batch, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of samples in the batch is the number of elements in _batch_.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector. 
*  _batch_: the collection of indexes representing the batch of samples. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
```cs
public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The sequence length is the number of elements in _sequence_. The created sequence is a new sequence.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector. 
*  _sequence_: the collection of indexes representing the sequence of samples. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence from a previous call to this method. The sequence length is the number of elements in _sequence_.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector. 
*  _sequence_: the collection of indexes representing the sequence of samples. 
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer collection of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the collection list of _batchOfSequences_. Each sequence in _batchOfSequences_ is a new sequence.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer collection of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the inner collection of _batchOfSequences_.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `IEnumerable` represents a collection of sequences with variable length, and the inner `IEnumerable` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
### The following methods create a Value object using sparse input. 
Currently the Compressed Sparse Column Format (CSC) is supported. The CSC format stores the matrix in column-major format, and the array containing the column indices is compressed. A matrix in CSC format is represented by the following parameters:
* _nonZeroValues_: the data array that holds all nonzero values of the matrix in column-major format.
* _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.   
* _colStarts_: the array that holds indices into the arrays _rowIndices_ and _nonZeroValues_.

A detailed description of the CSC format can be found [here](http://docs.nvidia.com/cuda/cusparse/index.html#compressed-sparse-column-format-csc).

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The created sequence is a new sequence.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _sampleShape_: the tensor shape of the Value. For sparse input, the tensor shape leading dimensionality must be the same as the total size of the tensor shape.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(NDShape sampleShape, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence from a previous call to this method.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _sampleShape_: the tensor shape of the Value. For sparse input, the tensor shape leading dimensionality must be the same as the total size of the tensor shape.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(int dimension, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The created sequence is a new sequence.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the number of columns in the sparse matrix.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cs
public static Value CreateSequence<T>(int dimension, int sequenceLength, int[] colStarts, int[] rowIndices, T[] nonZeroValues, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the number of columns in the sparse matrix.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
### The following methods create a Value object from NDArrayView. 

***
```cs
public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object based on a collection of NDArrayViews. Each sequence in _sequences_ is a new sequence.

Parameters:
*  _sampleShape_: the tensor shape of the Value being created.
*  _sequences_: a collection of sequences represented by NDArrayView. Each NDArrayView represents a sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.
    
```cs
public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
```

Creates a new Value object based on a collection of NDArrayViews. The sequenceStartFlags specifies whether a sequence is a new sequence or continuation of a previous sequence.

Parameters:
*  _sampleShape_: the tensor shape of the Value being created.
*  _sequences_: a collection of sequences represented by NDArrayView. Each NDArrayView represents a sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _sequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

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

[DataType](#enum-datatype) of the data contained in 'this' Value object.

***
```cs
public DeviceDescriptor Device { get; }
```

The descriptor of the device that 'this' Value resides on.

***
```cs
public NDShape Shape { get; }
```

The shape of 'this' Value.

***
```cs
public StroageFormat StorageFormat { get; }
```

The storage format of 'this' Value.

***
```cs
public bool IsSparse { get; }
```

A boolean value indicating whether 'this' Value contains data in sparse storage format.

***
```cs
public bool IsReadOnly { get; }
```

A boolean value indicating whether 'this' Value is read-only.

***
```cs
public int maskedCount { get; }
```

The number of masked/invalid values in 'this' Value.

***
```cs
public void CopyFrom(Value Source)
```

Copies the contents of `source` to 'this' Value. The shapes of the _Source_'s data and mask must be identical to 'this' Value's data and mask.

Parameter:
*  _Source_: The source object from which 'this' Value is copied.

***
```cs
public Value DeepClone()
```

Creates a new Value with newly allocated storage on the same device as 'this' Value and copies 'this' Value's contents into the newly allocated Value.

***
```cs
public Value DeepClone(bool readOnly)
```

Creates a new Value with newly allocated storage on the same device as 'this' Value and copies 'this' Value's contents into the newly allocated Value.

Parameter:
*  _readOnly_: the new Value object is read-only if this flag is `true`.

***
```cs
public Value Alias(bool readOnly)
```

Creates a new Value which is an alias of 'this' Value.
Parameter:
*  _readOnly_: the new Value object is read-only if this flag is `true`.

***
```cs
public IList<IList<T>> GetDenseData<T>(Variable outputVariable)
```

Gets the data stored in the Value object as a list of sequences with variable length in dense format. This method returns an IList<IList<T>>. Each element of the outer list represents a sequence. Each sequence, represented by IList<T>, contains a variable number of samples. Each sample consists of a fixed number of elements with type of 'T'. The number of elements is determined by the shape of outputVariable. The number of samples is the count of elements in IList<T> divided by the count of elements of the sample.

Parameter:
*  _outputVariable_: The variable that 'this' Value denotes to. The shape of the variable should match the shape of 'this' Value.

***
```cs
public IList<IList<int>> GetOneHotData(Variable outputVariable)
```

Gets the data stored in the Value object as a list of sequences with variable length in one-hot vector format. This method returns an IList<IList<T>>. Each element of the outer list represents a sequence. Each sequence, represented by IList<int>, contains a variable number of samples. Each sample is represented by an index to the one-hot vector. The number of samples is the count of elements in IList<int>.

Parameter:
*  _outputVariable_: The variable that 'this' Value denotes to. The size of the one-hot vector should match that defined in the variable.

***
### The following methods will be deprecated soon. Please use GetDenseData() and GetOneHotData() described above.

***
```cs
public void CopyVariableValueTo<T>(Variable outputVariable, List<List<T>> sequences)
```

Copies the data stored in the Value into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `List<T>`, contains a variable number of samples. Each sample consists of a fixed number of elements with type of _T_. The number of elements of a sample is determined by the shape of _outputVariable_. The shape of the variable should match the shape of the Value.

Parameters:
*  _outputVariable_: denotes the shape and dynamic axes when copying data from 'this' Value to the _sequences_. 
*  _sequences_: the output buffer used to store the data copied from the Value.

***
```cs
public void CopyVariableValueTo(Variable outputVariable, List<List<uint>> sequences
```

Copies the data stored in the Value object into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `List<uint>`, contains a variable number of samples. Each sample is represented by an index pointing to the non-zero value in the one-hot vector. The dimension size of the one-hot vector should match that defined in the _outputVariable_. 

Parameters:
* _outputVariable_: denotes the shape and dynamic axes when copying data from 'this' Value to the _sequences_. 
* _sequences_: the output buffer used to store the data copied from the Value.

## class Variable
Denotes a symbolic entity corresponding to the inputs and outputs of a [`Function`](#class-function).

The properties of `Variable` that are often used in evaluation include:

***
```cs
public string Name { get; }
```

Name of 'this' Variable.

***
```cs
public NDShape Shape { get; }
```

Shape of 'this' Variable.

***
```cs
public DataType DataTye { get; }
```

DataType(#enum-datatype) of the data that 'this' Variable represents.

***
```cs
public VariableKind Kind { get; }
```

The [VariableKind](#enum-variablekind) of 'this' Variable.

***
```cs
public bool IsSparse { get; }
```

A boolean value indicating whether 'this' Variable denotes sparse data.

***
```cs
public bool IsInput { get; }
```

A boolean value indicating whether 'this' Variable is an Input.

***
```cs
public bool IsOutput { get; }
```

A boolean value indicating whether 'this' Variable is an Output.

***
```cs
public bool IsParameter { get; }
```

A boolean value indicating whether 'this' Variable is a Parameter.

***
```cs
public bool IsConstant { get; }
```

A boolean value indicating whether 'this' Variable is a Constant.

***
```cs
public bool IsPlaceholder { get; }
```

A boolean value indicating whether 'this' Variable is a Placeholder.

***
```cs
public IList<Axis> DynamicAxes { get; }
```

Returns the dynamic axes of 'this' Variable.

***
```cs
public Function Owner { get; }
```

Returns the Function which 'this' Variable is an output of. Returns `null` when 'this' Variable is not of VariableKind Output.

## class DeviceDescriptor
Denotes a computation device instance.

The class `DeviceDescriptor` contains the following properties and methods:

***
```cs
public int Id { get; }
```

The Id of 'this' device. 

***
```cs
public DeviceKind Type { get; }
```

[DeviceKind](#enum-devicekind) of 'this' device.

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
*  _deviceId_: the CUDA device ID.

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

Parameter:
*  _numAxes_: the number of axes of the shape.
*  _dimension_: the dimension size, applied to each axis.

***
```cs
public NDShape(int numAxes)
```

Constructs a NDShape instance with the specified rank. The dimensionality in each axis is `NDShape.InferredDimension`.

Parameter:
*  _numAxes_: the number of axes of the shape.

***
```cs
public static NDShape CreateNDShape(IEnumerable<int> dimensions)
```

Creates a NDShape instance with specified dimensions.
Parameter:
*  _dimensions_: the dimension size of each axis.

***
```cs
public int Rank { get; }
```

The rank of 'this' NDShape.

***
```cs
public IList<int> Dimensions { get; }
```

The dimensions of 'this' NDShape. 

***
```cs
public int TotalSize { get; }
```

The total size of the rectangular shape that 'this' Shape denotes.

***
```cs
public int this[int key] { get; } 
```

Returns the dimension size of the specified axis.

***
```cs
public int IsUnknown { get; }
```

A boolean indicating if 'this' shape is the special Unknown shape.

*** 
```cs
public int HasInferredDimension { get; }
```

A boolean value indicating if the dimension size for any of the axes of 'this' shape is unknown/inferred (NDShape.InferredDimension).

***
```cs
public NDShape AppendShape(NDShape shape)
```

Creates and returns a new shape constructed by appending the dimensions of the specified 'shape' to 'this' shape's dimensions.

***
```cs
public NDShape SubShape(int beginAxisId, int endAxisId)
```

Creates and returns a new NDShape instance with the same dimensions as 'this' shape's specified axis range [beginAxisId, endAxisId).

***
```cs
public NDShape SubShape(int beginAxisId)
```

Creates and returns a new NDShape instance with the same dimensions as 'this' shape's axis range between the beginAxisId axis (inclusive) and the last axis (inclusive).

## class NDArrayView
Denotes a multi-dimensional writable or read-only array of elemental values. This type denotes a view and there may be multiple simultaneous views of the data underlying a NDArrayView instance. The underlying data is stored in sparse or dense format, and is located on a specific device.

***
```cs
public NDArrayView(NDShape viewShape, float[] dataBuffer, DeviceDescriptor device, bool readOnly = false)
public NDArrayView(NDShape viewShape, double[] dataBuffer, DeviceDescriptor device, bool readOnly = false)
```

Constructs a NDArrayView with the specified 'dataBuffer' in dense format as the backing storage on the specified device.

Parameter:
*  _viewShape_: the shape of the NDArrayView being created.
*  _dataBuffer_: the data values contained in the NDArrayView. The 'dataBuffer' must be at least as large as the total size of the specified 'viewShape' and must outlive the created NDArrayView object.
*  _device_: on which device the NDArrayView object should be created.
*  _readOnly_: the NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, float[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, double[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
```

Constructs a NDArrayView with the specified storage in sparse CSC format on the specified device. The specified sparse data must outlive the created NDArrayView object.

Parameter:
*  _viewShape_: the shape of the NDArrayView being created.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_. 
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix. The specified sparse data must outlive the created NDArrayView object. 
*  _device_: on which device the NDArrayView object should be created.
*  _readOnly_: the NDArrayView object is read-only if this flag is `true`.

***
```cs
public DeviceDescriptor Device
```

The DeviceDescriptor of the device that 'this' NDArrayView resides on.

***
```cs
public NDShape Shape
```

The shape of 'this' NDArrayView.

***
```cs
public DataType DataType
```

The DataType of the data that 'this' NDArrayView stores.

***
```cs
public StroageFormat StorageFormat
```

The storage format of 'this' NDArrayView.

***
```cs
public bool IsSparse
```

A boolean value indicating whether 'this' NDArrayView contains data in sparse storage format.

***
```cs
public bool IsReadOnly`

A boolean value indicating whether 'this' NDArrayView is read-only.

***
```cs
public NDArrayView DeepClone()
```

Creates a new NDArrayView with newly allocated storage on the same device as 'this' view and copies 'this' view's contents into the newly allocated view.

***
```cs
public NDArrayView DeepClone(bool readOnly)
```

Creates a new NDArrayView with newly allocated storage on the same device as 'this' view and copies 'this' view's contents into the newly allocated view.

Parameter:
*  _readOnly_: the new NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView DeepClone(DeviceDescriptor device, bool readOnly)
```

Creates a new NDArrayView with newly allocated storage on the specified device and copies 'this' view's contents into the newly allocated view.

Parameter:
*  _device_: on which device the new NDArrayView object should be created.
*  _readOnly_: the new NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView Alias(bool readOnly)
```

Creates a new NDArrayView which is an alias of 'this' view; i.e. a new view of the same shape as 'this' over the same underlying data.

Parameter:
*  _readOnly_: the new NDArrayView object is read-only if this flag is `true`.

***
```cs
public NDArrayView AsShape(NDShape newShape)
```

Creates a new NDArrayView which is an alias of 'this' view but with a new shape.

Parameter:
*  _newShape_: the shape of the new NDArrayView object.

***
```cs
public void CopyFrom(NDArrayView source)
```

Copies the contents of the 'source' NDArrayView to 'this' view. The shapes of the 'source' view and 'this' view must be identical.

Parameter:
*  _source_: the source NDArrayView whose content is copied to 'this' view.

***
```cs
public void ChangeDevice(DeviceDescriptor device)
```

Changes the device of 'this' NDArrayView to the specified device.

Parameter:
*  _device_: the target device of 'this' NDArrayView object.

## class Axis
Denotes an Axis of a [`Variable`](#class-variable). Besides the static axes corresponding to each of the axes of the Variable's shape, Variables of kind 'Input' and any 'Output' Variables dependent on an 'Input' Variable also have 2 additional dynamic axes whose dimensions are known only when the Variable is bound to actual data during compute (viz. sequence axis and batch axis denoting the axis along which multiple sequences are batched)

The following properties are defined in the class `Axis`

***
```cs
public string Name { get; }
```

Name of 'this' Axis. 

***
```cs
public bool IsStatic { get; }
```

Returns a boolean value indicating whether 'this' Axis corresponds to a static axis.

***
```cs
public bool IsDynamic { get; }
```

Returns a boolean value indicating whether 'this' Axis corresponds to a dynamic axis.

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
public static void SetTraceLevel(TraceLevel value)`

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


