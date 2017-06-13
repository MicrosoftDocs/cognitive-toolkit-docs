---
title:   CNTK Library Native Eval Interface
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/03/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:  cpp
---

# CNTK Library Native Eval Interface

The C++ CNTK Library for Evaluation is based on [CNTK Library API](./CNTK-Library-Managed-API.md). The following methods are used to evaluate a model.

***
## Load a model

***
```cpp
static FunctionPtr CNTK::Function::Load(const std::wstring& modelFile, const DeviceDescriptor& computeDevice = DeviceDescriptor::UseDefaultDevice()
```

This method loads a model file and returns the pointer to the Function that represents the loaded model. A Function in CNTK denotes a computation graph composed of primitive Functions or composite functions with zero or more input arguments and one or more outputs. The `modelFile` is the model file path. The `computeDevice` specifies the device to run evaluation.

***
```cpp
static FunctionPtr CNTK::Function::Load(char *modelBuffer, size_t modelBufferLength, const DeviceDescriptor& computeDevice = DeviceDescriptor::UseDefaultDevice()
```

This method loads a model from a memory buffer and returns the pointer to the Function that represents the loaded model. The `modelBuffer` points to the buffer containing the serialized model content, and the `modelBufferLength` is the buffer length. The `computeDevice` specifies the device to run evaluation.

***
## Evaluate a Function

***
```cpp
void CNTK::Function::Evaluate(const std::unordered_map<Variable, ValuePtr>& arguments, std::unordered_map<Variable, ValuePtr>& outputs, const DeviceDescriptor& computeDevice = DeviceDescriptor::UseDefaultDevice()
```

This method starts evaluation of the `this` Function representing the model with specified parameters. The `arguments` contains values of all input variables required for evaluation, and the `outputs` store the values of output variables. The storage of output values can either be pre-allocated by the caller, or by the system if the ValuePtr mapping is passed as null by the caller. The `computeDevice` specifies the device to run evaluation.

***
## Create a Value using dense input

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateBatch(const NDShape& sampleShape, const std::vector<ElementType>& batchData, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a batch of samples. The number of samples in the batch is the number of elements in _batchData_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). The created Value object contains a copy of the specified data in _batchData_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value object.
*  _batchData_: the data to be contained in the Value object.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value object is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(const NDShape& sampleShape, const std::vector<ElementType>& sequenceData, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified data in _sequenceData_. The sequence length is the number of elements in _sequenceData_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). The created sequence is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _sequenceData_: the data to be contained in the Value.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(const NDShape& sampleShape, const std::vector<ElementType>& sequenceData, bool sequenceStartFlag, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified data in _sequenceData_. The sequence length is the number of elements in _sequenceData_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _sequenceData_: the data to be contained in the Value.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(const NDShape& sampleShape, const std::vector<std::vector<ElementType>>& batchOfSequences, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). Each sequence in _batchOfSequences_ is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(const NDShape& sampleShape, const std::vector<std::vector<ElementType>>& batchOfSequences, const std::vector<bool>& sequenceStartFlags, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
## Create a Value using one-hot vector input

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateBatch(size_t dimension, const std::vector<size_t>& batchData, const DeviceDescriptor& device, bool readOnly = false)
```

Creates a new Value object containing a batch of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of samples in the batch is the number of elements in _batchData_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchData_: the collection of indexes representing the batch of samples.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(size_t dimension, const std::vector<size_t>& sequenceData, const DeviceDescriptor& device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The sequence length is the number of elements in _sequenceData_.Each sequence is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _sequenceData_: the collection of indexes representing the sequence of samples.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(size_t dimension, const std::vector<size_t>& sequenceData, bool sequenceStartFlag, const DeviceDescriptor& device, bool readOnly = false)
```

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The seqStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method. The sequence length is the number of elements in _sequenceData_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _sequenceData_: the collection of indexes representing the sequence of samples.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(size_t dimension, const std::vector<std::vector<size_t>>& batchOfSequences, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer list of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the inner list of _batchOfSequences_. Each sequence in _batchOfSequences_ is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(size_t dimension, const std::vector<std::vector<size_t>>& batchOfSequences, const std::vector<bool>& sequenceStartFlags, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer list of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the inner list of _batchOfSequences_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
## Create a Value using sparse CSC input

Currently the Compressed Sparse Column Format (CSC) is supported. The CSC format stores the matrix in column-major format, and the array containing the column indices is compressed. A matrix in CSC format is represented by the following parameters:
* _nonZeroValues_: the data array that holds all nonzero values of the matrix in column-major format.
* _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
* _colStarts_: the array that holds indices into the arrays _rowIndices_ and _nonZeroValues_.

A detailed description of the CSC format can be found [here](http://docs.nvidia.com/cuda/cusparse/index.html#compressed-sparse-column-format-csc).

***
```cpp
template <typename ElementType> static ValuePtr  CNTK::Value::CreateSequence(const NDShape& sampleShape, size_t sequenceLength, const SparseIndexType* colStarts, const SparseIndexType* rowIndices, const ElementType* nonZeroValues, size_t numNonZeroValues, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The created sequence is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value. For sparse input, the tensor shape leading dimensionality must be the same as the total size of the tensor shape.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _numNonZeroValues_: the number of nonzero values in the sparse matrix.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr  CNTK::Value::CreateSequence(const NDShape& sampleShape, size_t sequenceLength, const SparseIndexType* colStarts, const SparseIndexType* rowIndices, const ElementType* nonZeroValues, size_t numNonZeroValues, bool sequenceStartFlag, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence from a previous call to this method. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value. For sparse input, the tensor shape leading dimensionality must be the same as the total size of the tensor shape.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _numNonZeroValues_: the number of nonzero values in the sparse matrix.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr  CNTK::Value::CreateSequence(size_t dimension, size_t sequenceLength, const SparseIndexType* colStarts, const SparseIndexType* rowIndices, const ElementType* nonZeroValues, size_t numNonZeroValues, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The created sequence is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _numNonZeroValues_: the number of nonzero values in the sparse matrix.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
template <typename ElementType> static ValuePtr  CNTK::Value::CreateSequence(size_t dimension, size_t sequenceLength, const SparseIndexType* colStarts, const SparseIndexType* rowIndices, const ElementType* nonZeroValues, size_t numNonZeroValues, bool sequenceStartFlag, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object containing a sequence of samples using CSC sparse input format. The sequence length is the number of rows of the sparse matrix. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _sequenceLength_: the sequence length, which is also the number of rows in the sparse matrix.
*  _colStarts_: the array holds indices for each column into the arrays _rowIndices_ and _nonZeroValues_.
*  _rowIndices_: the array that contains the row indices of the corresponding elements in array _nonZeroValues_.
*  _nonZeroValues_: the array that holds all nonzero values in the sparse matrix.
*  _numNonZeroValues_: the number of nonzero values in the sparse matrix.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
## Create a Value from NDArrayView

***
```cpp
static ValuePtr  CNTK::Value::Create(const NDShape& sampleShape, const std::vector<NDArrayViewPtr>& sequences, const DeviceDescriptor& device, bool readOnly = false
```

Creates a new Value object based on a collection of NDArrayViews. Each sequence in _sequences_ is a new sequence.

Parameters:
*  _sampleShape_: the tensor shape of the Value being created.
*  _sequences_: a collection of sequences represented by NDArrayView. Each NDArrayView represents a sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
```cpp
static ValuePtr  CNTK::Value::Create(const NDShape& sampleShape, const std::vector<NDArrayViewPtr>& sequences, const std::vector<bool>& sequenceStartFlags, const DeviceDescriptor& device, bool readOnly, bool createNewCopy
```

Creates a new Value object based on a collection of NDArrayViews. The sequenceStartFlags specifies whether a sequence is a new sequence or continuation of a previous sequence.

Parameters:
*  _sampleShape_: the tensor shape of the Value being created.
*  _sequences_: a collection of sequences represented by NDArrayView. Each NDArrayView represents a sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _sequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
## Copy data from a Value object

***
```cpp
template <typename ElementType> void CNTK::Value::CopyVariableValueTo(const Variable& outputVariable, std::vector<std::vector<ElementType>>& sequences
```

Copies the data stored in the Value into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `vector<ElementType>`, contains a variable number of samples. Each sample consists of a fixed number of elements with type of _ElementType_. The number of elements of a sample is determined by the shape of _outputVariable_. The shape of the variable should match the shape of the Value. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _outputVariable_: denotes the shape and dynamic axes when copying data from this Value to the _sequences_.
*  _sequences_: the output buffer used to store the data copied from the Value.

***
```cpp
void CNTK::Value::CopyVariableValueTo(const Variable& outputVariable, std::vector<std::vector<size_t>>& sequences
```

Copies the data stored in the Value object into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `vector<size_t>`, contains a variable number of samples. Each sample is represented by an index pointing to the non-zero value in the one-hot vector. The dimension size of the one-hot vector should match that defined in the _outputVariable_.

Parameters:
* _outputVariable_: denotes the shape and dynamic axes when copying data from this Value to the _sequences_.
* _sequences_: the output buffer used to store the data copied from the Value.

***
## Helper functions to manipulate the Function to be evaluated

***
```cpp
FunctionPtr CNTK::Function::Clone(ParameterCloningMethod parameterCloneMethod = ParameterCloningMethod::Clone, const std::unordered_map<Variable, Variable>& replacements = {}) const
```

For evaluation, this method is called to create a cloned Function which can then be used by another thread to evaluate the same model. For this purpose, the `parameterCloneMethod` should be set to its default value `ParameterCloningMethod::Share`. The parameter `replacements` specifies any variable replacements that are applied in the cloned Function instance, and is usually not needed for evaluation.

***
```cpp
FunctionPtr CNTK::Function::FindByName(const std::wstring& name, bool nestedSearchInsideBlockFunction = false
```

Find a function with the given _name_ in the Function graph underlying 'this' Function. If more than one function with the same name exists, an exception is thrown. If _nestedSearchInsideBlockFunction_ is true, all functions inside block functions are also searched.

***
```cpp
std::vector<FunctionPtr> CNTK::Function::FindAllWithName(const std::wstring& name, bool nestedSearchInsideBlockFunction = false
```

Find a list of functions with the given _name_ in the Function graph underlying 'this' Function. If _nestedSearchInsideBlockFunction_ is true, all functions inside block functions are also searched.

***
```cpp
FunctionPtr CNTK::Combine(const std::vector<Variable>& operands, const std::wstring& name = L""
```

 Create a new Function instance which combines the outputs of the specified list of 'operands' of Functions. The 'Outputs' of the new 'Function' are union of the 'Outputs' of each of the specified 'operands' Functions. As an example, when creating a classification model, typically the CrossEntropy loss Function and the ClassificationError Function comprise the roots of the computation graph which can be "Combine"d to create a single Function with 2 outputs; viz. CrossEntropy loss and ClassificationError output.

***
```cpp
FunctionPtr CNTK::AsComposite(const FunctionPtr& rootFunction, const std::wstring& name = L""
```

Creates a composite Function that has the specified `_rootFunction_` as its root. The composite denotes a higher-level Function encapsulating the entire graph of Functions underlying the specified `rootFunction`.

***
```cpp
FunctionPtr CNTK::Alias(const Variable& operand, const std::wstring& name = L""
```

Creates a new Function instance which is just an alias of the specified `_operand_`.

***

Please refer to [CNTKLibrary.h](https://github.com/Microsoft/CNTK/blob/master/Source/CNTKv2LibraryDll/API/CNTKLibrary.h) for details of data types used by the functions above.

## Examples
The C++ examples [`CNTKLibraryCPPEvalCPUOnlyExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [`CNTKLibraryCPPEvalGPUExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) demonstrate the usage of CNTK Library for evaluation. The samples also show how to do multiple evaluations in parallel using multiple threads and share model parameters among threads.
