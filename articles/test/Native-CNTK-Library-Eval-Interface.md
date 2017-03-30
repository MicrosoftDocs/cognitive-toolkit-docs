The C++ CNTK Library for Evaluation is based on [CNTK Library API](https://github.com/microsoft/cntk/wiki/CNTK-Library-API). The following methods are used to evaluate a model.

***
`static FunctionPtr CNTK::Function::LoadModel(const std::wstring& modelFile, const DeviceDescriptor& computeDevice = DeviceDescriptor::UseDefaultDevice())`

This method loads a model file and returns the pointer to the Function that represents the loaded model. A Function in CNTK denotes a computation graph composed of primitive Functions or composite functions with zero or more input arguments and one or more outputs. The `modelFile` is the model file path. The `computeDevice` specifies the device to run evaluation.

***
`static FunctionPtr CNTK::Function::LoadModel(char *modelBuffer, size_t modelBufferLength, const DeviceDescriptor& computeDevice = DeviceDescriptor::UseDefaultDevice())`

This method loads a model from a memory buffer and returns the pointer to the Function that represents the loaded model. The `modelBuffer` points to the buffer containing the serialized model content, and the `modelBufferLength` is the buffer length. The `computeDevice` specifies the device to run evaluation.

***
`void CNTK::Function::Evaluate(const std::unordered_map<Variable, ValuePtr>& arguments, std::unordered_map<Variable, ValuePtr>& outputs, const DeviceDescriptor& computeDevice = DeviceDescriptor::UseDefaultDevice())`

This method starts evaluation of the `this` Function representing the model with specificed parameters. The `arguments` contains values of all input variables required for evaluation, and the `outputs` store the values of output variables. The storage of output values can either be pre-allocated by the caller, or by the system if the ValuePtr mapping is passed as null by the caller. The `computeDevice` specifies the device to run evaluation.

***
`FunctionPtr CNTK::Function::Clone(ParameterCloningMethod parameterCloneMethod, const std::unordered_map<Variable, Variable>& replacements) const`

This method clones `this` Function. For evaluation, this method is called to create a cloned Function which can then be used by another thread to evaluate the same model. For this purpose, the `parameterCloneMethod` is set to its default value `cloned`. The parameter `replacements` specifies any variable replacements that are applied in the cloned Function instance, and is usually not needed for evaluation. 

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateBatch(const NDShape& sampleShape, const std::vector<ElementType>& batchData, const DeviceDescriptor& device, bool readOnly = false)`

Creates a new Value object containing a batch of samples. The number of samples in the batch is the number of elements in _batchData_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). The created Value object contains a copy of the specified data in _batchData_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.
 
Parameters:
*  _sampleShape_: the tensor shape of the Value object. 
*  _batchData_: the data to be contained in the Value object. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value object is read-only if this flag is `true`. 

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(const NDShape& sampleShape, const std::vector<ElementType>& sequenceData, const DeviceDescriptor& device, bool readOnly = false)`

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified data in _sequenceData_. The sequence length is the number of elements in _sequenceData_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). The created sequece is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.
 
Parameters: 
*  _sampleShape_: the tensor shape of the Value. 
*  _sequenceData_: the data to be contained in the Value.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(const NDShape& sampleShape, const std::vector<ElementType>& sequenceData, bool sequenceStartFlag, const DeviceDescriptor& device, bool readOnly = false)`

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified data in _sequenceData_. The sequence length is the number of elements in _sequenceData_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.
 
Parameters: 
*  _sampleShape_: the tensor shape of the Value. 
*  _sequenceData_: the data to be contained in the Value.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(const NDShape& sampleShape, const std::vector<std::vector<ElementType>>& batchOfSequences, const DeviceDescriptor& device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). Each sequence in _batchOfSequences_ is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(const NDShape& sampleShape, const std::vector<std::vector<ElementType>>& batchOfSequences, const std::vector<bool>& sequenceStartFlags, const DeviceDescriptor& device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _sampleShape_ (A runtime error occurs if the remainder is not zero). _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateBatch(size_t dimension, const std::vector<size_t>& batchData, const DeviceDescriptor& device, bool readOnly = false);`

Creates a new Value object containing a batch of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of samples in the batch is the number of elements in _batchData_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector. 
*  _batchData_: the collection of indexes representing the batch of samples. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(size_t dimension, const std::vector<size_t>& sequenceData, const DeviceDescriptor& device, bool readOnly = false);`

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The sequence length is the number of elements in _sequenceData_.Each sequence is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:  
*  _dimension_: the size of dimension of the one-hot vector. 
*  _sequenceData_: the collection of indexes representing the sequence of samples.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateSequence(size_t dimension, const std::vector<size_t>& sequenceData, bool sequenceStartFlag, const DeviceDescriptor& device, bool readOnly = false);`

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The seqStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method. The sequence length is the number of elements in _sequenceData_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:  
*  _dimension_: the size of dimension of the one-hot vector.
*  _sequenceData_: the collection of indexes representing the sequence of samples.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(size_t dimension, const std::vector<std::vector<size_t>>& batchOfSequences, const DeviceDescriptor& device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer list of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the inner list of _batchOfSequences_. Each sequence in _batchOfSequences_ is a new sequence. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`template <typename ElementType> static ValuePtr CNTK::Value::CreateBatchOfSequences(size_t dimension, const std::vector<std::vector<size_t>>& batchOfSequences, const std::vector<bool>& sequenceStartFlags, const DeviceDescriptor& device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer list of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the inner list of _batchOfSequences_. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `vector` represents a collection of sequences with variable length, and the inner `vector` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`template <typename ElementType> void CNTK::Value::CopyVariableValueTo(const Variable& outputVariable, std::vector<std::vector<ElementType>>& sequences)`

Copies the data stored in the Value into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `vector<ElementType>`, contains a variable number of samples. Each sample consits of a fixed number of elements with type of _ElementType_. The number of elements of a sample is determined by the shape of _outputVariable_. The shape of the variable should match the shape of the Value. _ElementType_ is the data type of the created Value object. Currently, `float` and `double` are supported.

Parameters:
*  _outputVariable_: denotes the shape and dynamic axes when copying data from this Value to the _sequences_. 
*  _sequences_: the output buffer used to store the data copied from the Value.

***
`void CNTK::Value::CopyVariableValueTo(const Variable& outputVariable, std::vector<std::vector<size_t>>& sequences)`

Copies the data stored in the Value object into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `vector<size_t>`, contains a variable number of samples. Each sample is represented by an index pointing to the non-zero value in the one-hot vector. The dimension size of the one-hot vector should match that defined in the _outputVariable_. 

Parameters:
* _outputVariable_: denotes the shape and dynamic axes when copying data from this Value to the _sequences_. 
* _sequences_: the output buffer used to store the data copied from the Value.

Please refer to [CNTKLibrary.h](https://github.com/Microsoft/CNTK/blob/master/Source/CNTKv2LibraryDll/API/CNTKLibrary.h) for details of data types used by the functions above. 

## Examples
The C++ examples [`CNTKLibraryCPPEvalCPUOnlyExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalCPUOnlyExamples) and [`CNTKLibraryCPPEvalGPUExamples`](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CNTKLibraryCPPEvalGPUExamples) demonstrate the usage of CNTK Library for evaluation. The samples also show how to do multiple evaluations in parallel using multiple threads and share model parameters among threads. 
