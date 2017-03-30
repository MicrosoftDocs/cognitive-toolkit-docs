The CNTK Library Managed Eval API is implemented in C#, and can be consumend by C# and other .NET languages. The page [Using the CNTK Library Managed API](https://github.com/Microsoft/CNTK/wiki/CNTK-Library-Evaluation-on-Windows#using-the-cntk-library-managed-api) presents how to use this API in your application. The following sections describe the classes and methods of the CNTK Library Managed Eval API.

## `class Function`

A `Function` denotes a symbolic computation with zero or more input arguments and one or more outputs. A Function may be primitive or composite (comprised of other Function instances whose inputs and outputs are wired together). A Function effectively is a computation graph composed of other primitive Functions (denoting computation) as nodes and [`Variable`](./CNTK-Library-Managed-API#class-variable) objects (denoting data) as the edges and leaves of the graph.

The `Function` class contains the following peroperties and methods that are relevant to evaluation:

***
`public string Name { get; }`

Name of this Function.

***
`public List<Variable> Arguments { get; }`

List of all input Variables of this Function that are not of type Parameter or Constant. These Variables are required inputs in order to to compute the outputs of the Function. 

***
`public Variable Output { get; }`

The single output Variable of this Function, if there is only one. Otherwise a runtime exception is raised on access. 

***
 `public List<Variable> Outputs { get; }`

List of all output Variables of this Function.

***
`public Function RootFunction { get; }`

Returns the primitive Function at the root of the graph of Functions underlying this Function. If this Function itself is a primitive Function, then (this->RootFunction() == this).

***
`public string OpName { get; }`

The name of the operation that this Function denotes.

***
`pulic bool IsComposite { get; }`

A boolean value indicating whether this Function is a composite Function.

***
`public bool IsPrimitive { get; }`

A boolean value indicating whether this Function is a primitive Function.

***
`public bool IsBlock { get; }`

A boolean value indicating whether this Function is a block Function.

***
`public static Function LoadModel(string modelFile, DeviceDescriptor computeDevice)`

Load a Function from a model file.

Parameters:   
*  _modelFile_: the path to the model file.
*  _computeDevice_: the device on which the Function is loaded.

***
`public static Function LoadModel(byte[] modelBuffer, DeviceDescriptor computeDevice)`

Load a Function from a memory buffer representing a model.

Parameters:   
*  _modelBuffer_: the byte array contains the serialized model content.
*  _computeDevice_: the device on which the Function is loaded.

***
`public void Evaluate(Dictionary<Variable, Value> arguments, Dictionary<Variable, Value> outputs, DeviceDescriptor computeDevice)`

Evaluates the Function using the specified _arguments_ as input. It uses provided [`Values`](./CNTK-Library-Managed-API#class-value) corresponding to each input variable specified in the _arguments_ dictionary to compute the outputs of the Function. The outputs are stored in the output Values corresponding to the output variables in the _outputs_ dictionary.

Parameters:
*  _arguments_: the input variables and their values of the Function.
*  _outputs_: store the output values for each output variable. The caller may specify the Value object for storing the output or pass _null_ in which case CNTK Library allocates the actual storage for the output value. In both cases, on return the Value object contains the result of the corresponding output of the Function.
*  _computeDevice_: the device on which the compuation is executued. It should be aligned with the device on which the model is loaded.

## `class Value`
A `Value` is a multi-dimensional array with an optional mask and is the actual data fed into or produced from a computation.

Properties:

***
`public DataType DataType { get; }`

The data type of the data contained in this Value object.

***
`public StroageFormat StorageFormat { get; }`

The storage format of this Value. 

***
`public NDShape Shape { get; }`

The shape of this Value.

***
`public bool IsSparse { get; }`

A boolean value indicating whether this Value contains data in sparse storage format.

***
`public bool IsReadOnly { get; }`

A boolean value indicating whether this Value is read-only

***
`public void CopyFrom(Value Source)`

Copies the contents of `source` to this Value. The shapes of the _source_'s data and mask must be identical to this Value's data and mask.
 
Parameter:
*  _Source_: The source object from which this 'Value' is copied.

***
`public static Value CreateBatch<T>(NDShape sampleShape, List<T> batch, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a batch of samples. The number of samples in the batch is the number of elements in _batch_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero). The created Value object contains a copy of the specified data in _batch_.
 
Parameters:  
*  _sampleShape_: the tensor shape of the Value object. 
*  _batch_: the data to be contained in the Value object. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value object is read-only if this flag is `true`. 

***
`public static Value CreateSequence<T>(NDShape sampleShape, List<T> sequence, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified data in _sequence_. The sequence length is the number of elements in _sequence_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero). The created sequence is a new sequence.
 
Parameters: 
*  _sampleShape_: the tensor shape of the Value. 
*  _sequence_: the data to be contained in the Value. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
`public static Value CreateSequence<T>(NDShape sampleShape, List<T> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a sequence of samples. The created Value object contains a copy of the specified _sequence_ data. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method. The sequence length is the number of elements in _sequence_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero).

Parameters:   
*  _sampleShape_: the tensor shape of the Value. 
*  _sequence_: the data to be contained in the Value.
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
`public static Value CreateBatchOfSequences<T>(NDShape sampleShape, List<List<T>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero). Each sequence in _batchOfSequences_ is a new sequence.

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `List` represents a collection of sequences with variable length, and the inner `List` represents each individual sequence.
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`public static Value CreateBatchOfSequences<T>(NDShape sampleShape, List<List<T>> batchOfSequences, List<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. The created Value object contains a copy of the specified data in _batchOfSequences_. The number of sequences in the batch is the size of _batchOfSequences_. The length of each sequence is the number of elements in the corresponding sequence of _batchOfSequences_ divided by the size of _shape_ (A runtime error occurs if the remainder is not zero).

Parameters:
*  _sampleShape_: the tensor shape of the Value.
*  _batchOfSequences_: the data to be stored in the Value. The outer `List` represents a collection of sequences with variable length, and the inner `List` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`public static Value CreateBatch<T>(uint dimension, List<uint> batch, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a batch of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of samples in the batch is the number of elements in _batch_.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector. 
*  _batch_: the collection of indexes representing the batch of samples. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`. 

***
`public static Value CreateSequence<T>(uint dimension, List<uint> sequence, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The sequence length is the number of elements in _sequence_.Each sequence is a new sequence.

Parameters:  
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector. 
*  _sequence_: the collection of indexes representing the sequence of samples. 
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`public static Value CreateSequence<T>(uint dimension, List<uint> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a sequence of samples. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The sequenceStartFlag specifies whether this sequence is a new sequence or continuation of a previous sequence at the same index in the sequences list from a previous call to this method. The sequence length is the number of elements in _sequence_.

Parameters:  
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector. 
*  _sequence_: the collection of indexes representing the sequence of samples. 
*  _sequenceStartFlag_: `true` indicates that it is a new sequence. `false` means a continuation of a previous sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`public static Value CreateBatchOfSequences<T>(uint dimension, List<List<uint>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer list of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the inner list of _batchOfSequences_. Each sequence in _batchOfSequences_ is a new sequence.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `List` represents a collection of sequences with variable length, and the inner `List` represents each individual sequence.
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`public static Value CreateBatchOfSequences<T>(uint dimension, List<List<uint>> batchOfSequences, List<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)`

Creates a new Value object containing a batch of variable length sequences. Each sample is represented by an index value that points to the non-zero value in the one-hot vector of _dimension_ elements. The number of sequences is the number of elements in the outer list of _batchOfSequences_. The length of each sequence is the number of elements of the corresponding sequence in the inner list of _batchOfSequences_.

Parameters:
*  _T_: data type of the created Value object. Currently, `float` and `double` are supported.
*  _dimension_: the size of dimension of the one-hot vector.
*  _batchOfSequences_: the collection of indexes representing sequences of samples. The outer `List` represents a collection of sequences with variable length, and the inner `List` represents each individual sequence.
*  _sequenceStartFlags_: A collection of boolean values. Each element represents whether the corresponding sequence in _batchOfSequences_ is a new sequence (in case of `true`) or a continuation of a previous sequence (in case of `false`).
*  _device_: on which device the Value object should be created.
*  _readOnly_: the Value is read-only if this flag is `true`.

***
`public void CopyVariableValueTo<T>(Variable outputVariable, List<List<T>> sequences)`

Copies the data stored in the Value into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `List<T>`, contains a variable number of samples. Each sample consits of a fixed number of elements with type of _T_. The number of elements of a sample is determined by the shape of _outputVariable_. The shape of the variable should match the shape of the Value.

Parameters:
*  _outputVariable_: denotes the shape and dynamic axes when copying data from this Value to the _sequences_. 
*  _sequences_: the output buffer used to store the data copied from the Value.

***
`public void CopyVariableValueTo(Variable outputVariable, List<List<uint>> sequences`

Copies the data stored in the Value object into the buffer provided by _sequences_. The _sequences_ is a list of sequences with variable length. The number of items contained in the outer list of _sequences_ is the number of sequences in the Value. Each element of the outer list represents a sequence. Each sequence, represented by `List<uint>`, contains a variable number of samples. Each sample is represented by an index pointing to the non-zero value in the one-hot vector. The dimension size of the one-hot vector should match that defined in the _outputVariable_. 

Parameters:
* _outputVariable_: denotes the shape and dynamic axes when copying data from this Value to the _sequences_. 
* _sequences_: the output buffer used to store the data copied from the Value.

## `class Variable`
Denotes a symbolic entity corresponding to the inputs and outputs of a [`Function`](./CNTK-Library-Managed-API#class-function).

The properties of `Variable` that are often used in evaluation include:

***
`public string Name { get; }`

Name of this Variable.

***
`public NDShape Shape { get; }`

Shape of this Variable.

***
`public DataType DataTye { get; }`

The DataType of the data that this Variable represents.

***
`public VariableKind Kind { get; }`

The VariableKind of this Varaiable.

***
`public bool IsSparse { get; }`

A boolean value indicating whether this Variable denotes sparse data.

***
`public bool IsInput { get; }`

A boolean value indicating whether this Variable is an Input.

***
`public bool IsOutput { get; }`

A boolean value indicating whether this Variable is an Output.

***
`public bool IsParameter { get; }`

A boolean value indicating whether this Variable is a Parameter.

***
`public bool IsConstant { get; }`

A boolean value indicating whether this Variable is a Constant.

***
`public bool IsPlaceholder { get; }`

A boolean value indicating whether this Variable is a Placeholder.

***
`public List<Axis> DynamicAxes { get; }`

Returns the dynamic axes of this Variable.

***
`public Function Owner { get; }`

Returns the Function which this Variable is an output of. Returns `null` when this Variable is not of VariableKind Output.

## `class NDShape`
Denotes a multi-dimensional rectangualar shape.

The following properties are available.

***
`public uint Rank { get; }`

The rank of this NDShape.

***
`public List<uint> Dimensions { get; }`

The dimensions of this NDShape. 

***
`public uint TotalSize { get; }`

The total size of the rectangular shape that this Shape denotes.

***
`public uint this[int key] { get; } `

Returns the dimension size of the specified axis.

## `public enum VariableKind`
Enumeration type denoting the kind of a symbolic [`Variable`](./CNTK-Library-Managed-API#class-variable) object

***
`{`        
`Input = 0,`  
`Output = 1,`   
`Parameter = 2,`   
`Constant = 3,`   
`Placeholder = 4`   
`}`   

## `public enum DataType`
Enumeration type denoting data type of symbolic data entities or actual data.

***
`{`    
`Unknown = 0,`   
`Float = 1,`  
`Double = 2`   
`}`   

## `class Axis`
Denotes an Axis of a [`Variable`](./CNTK-Library-Managed-API#class-variable). Besides the static axes corresponding to each of the axes of the Variable's shape, Variables of kind 'Input' and any 'Output' Variables dependent on an 'Input' Variable also have 2 additional dynamic axes whose dimensions are known only when the Variable is bound to actual data during compute (viz. sequence axis and batch axis denoting the axis along which multiple sequences are batched)

The following properties are defined in the class `Axis`

***
`public string Name { get; }`

Name of this Axis. 

***
`public bool IsStatic { get; }`

Returns a boolean value indicating whether this Axis corresponds to a static axis.

***
`public bool IsDynamic { get; }`

Returns a boolean value indicating whether this Axis corresponds to a dynamic axis.

## `class DeviceDescriptor`
Denotes a computation device instance.

The class `DeviceDescriptor` contains the following properties and methods:

***
`public uint Id { get; }`

The Id of this device. 

***
`public DeviceKind Type { get; }`

The DeviceKind of this device.

***
`public static DeviceDescriptor CPUDevice { get; }`

The DeviceDescriptor representing the CPU device on the local system.

***
`public static DeviceDescriptor GPUDevice(uint deviceId)`

Gets the DeviceDescriptor of the GPU device on the local system with the specified CUDA device ID.
 
Parameter:
*  _deviceId_: the CUDA device ID.

## `public enum DeviceKind`
Enumeration type denoting the type of a computation device.

***
`{`      
`CPU,` // A CPU device.   
`GPU`  // A GPU device.   
`}` 





