---
title:   Breaking changes in Master compared to beta15
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/05/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Breaking changes in Master compared to beta15

After we shipped beta15, we have made some breaking changes based on your feedback. We changed APIs, refactored codes, deprecated old APUs, and made changes to namespaces to create a more logical hierarchy. The changes are part of our Release Candidate 1. Our goal is to have as few as possible breaking changes as we approach now our final Cognitive Toolkit 2.0 release.

These are the current breaking changes:

## Python API

- NDArrayView's data() method is now a property 
- Namespaces:
    - Readers are exposed through `cntk.io` namespace,  they no longer imported to the root `cntk` namespace.
    - Several functionalities that were under `cntk.utils` are now refactored (removed) into their suitable namespace. Some of the popular ones are
        - Print progress has moved to `cntk.logging`.
        - Print graph or finding nodes by names moved from `cntk.graph` to `CNTK.logging.graph`
        - All debugging APIs have moved to `cntk.debugging`.
        - Move profiler APIs to `cntk.debugging`.
        - `one_hot` function is now a static method in Value class.
        - `value_to_seq` and `value_variable_to_seq` functions are now a method in Value `class` and renamed to `to_seq`.
    - Losses and metrics have moved to `cntk.losses` and `cntk.metrics` respectively.
    - Former `cntk.learner` namespace is replaced by `cntk.learners` with all learners moved under `cntk.learners` namespace. 
    - trainer, training_session, and distributed have moved to `cntk.train` namespace.
    - variables module has moved to cntk root namespace.
    - Layers APIs aren't loaded by default, so you need `from cntk.layers import *`.
    - `Input` is gone use `cntk.input` or `cntk.sequence.input` for recurrent network.
    - `Placeholder` is gone use `cntk.placeholder`
    - `Parameter` and `Constant` moved to CNTK namespace.
 
- Evaluating a node that has only the batch axis and no sequence axis, previously always returned the result containing a sequence dimension. This was fixed, i.e., it will now only output the batch axis.
- Evaluating a node that has a sequence axis, previously returned a list of NumPy arrays when the sequences were of different length, but a single NumPy array containing all sequences, when the sequences were of same length (most of the time, because the sequence axis was not used at all, resulting in all sequences having only one element). This was inconsistent is now fixed, such that whenever a sequence axis is present, the output is a Python list. If your program breaks because of this change, it might be a sign that you specified your input with a sequence axis (the default in `input_variable()`), but are not using it.
- `adam_sgd()` is deprecated:
	- If you used `adam_sgd()` with `low_memory=True` or didn't specify `low_memory` at all, switch to `fsadagrad()`.
	- Otherwise, use `adam()`, which matches the published ADAM method.
- `upper_pad` and `lower_pad` are gone.
- `MinibatchData.value` is deprecated, because it encourages inefficient usage patterns. Instead, `MinibatchData.data` now returns a `Value` instance, which does not need to be converted to NumPy when being passed to `eval()`, `forward()` or `train_minibatch()`.
- Automatic conversion to NumPy via `np.asarray(cntk_obj)` is being replaced by `asarray()`/`as_sequences()` member function of those objects
  - `asarray()` returns dense or sparse (instance of `scipy.csr_matrix`) versions of the underlying data
    - supported by `Constant`, `Parameter`, `NDArrayView`, `Value`, and `MinibatchData`
  - `as_sequences(var)` returns a Python list of sequences whose elements are NumPy arrays (according to var's dynamic and static axes)
    - supported by `Value` and `MinibatchData` 
  - The reasons for this change are that `np.assarray()`
    - always returns NumPy arrays, so no sequences could be supported
    - only supports dense arrays
    - does not allow to throw exceptions from within the array interface
- Deprecated parameters were removed from `training_session`. The progress printer should be now passed directly to the trainer, not to the training session. To configure different aspects of the training session, please use configuration objects. In the new version CNTK supports:
    - checkpoint configuration
    - cross validation configuration
    - test configuration

## C#/.NET CNTK Library API
- New file names of CNTK binaries. It only affects users who directly use CNTK libraries from binary drop packages. No changes are required for users who use NuGet packages for C++/C#.

- The following C# methods have been moved to new classes:
	- CNTKLib.Alias() => Function.Alias()
	- CNTKLib.AsComposite() => Function.AsComposite()
	- CNTKLib.Combine() => Function.Combine()
	- CNTKLib.SetMaxNumCPUThreads() => Utils.SetMaxNumCPUThreads()
	- CNTKLib.GetMaxNumCPUThreads() => Utils.GetMaxNumCPUThreads()

- The following C# API changes require adaptation in applications, mainly for simplification of API signature and type change from uint to int to make API CLS-compliant.
	- class NDShape
		- public NDShape(int numAxes, int dimension)
		- public NDShape(int numAxes)
		- public int Rank { get; }
		- public IList<int> Dimensions { get; }
		- public int TotalSize { get; }
		- public int this[int key] { get; }
		- public NDShape SubShape(int beginAxisId, int endAxisId)
		- public NDShape SubShape(int beginAxisId)
		- public static NDShape CreateNDShape(IEnumerable<int> dimensions)
	- class DeviceDescriptor
		- public int Id { get; }
		- public static DeviceDescriptor GPUDevice(int deviceId)
	- class NDArrayView
		- public NDArrayView(NDShape viewShape, float[] dataBuffer, DeviceDescriptor device, bool readOnly = false)
		- public NDArrayView(NDShape viewShape, double[] dataBuffer, DeviceDescriptor device, bool readOnly = false)
		- public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, float[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
		- public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, double[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
	- class NDMask
		- public int MaskedCount { get; }
		- public void InvalidateSection(IEnumerable<int> sectionOffset, NDShape sectionShape)
		- public void MarkSequenceBegin(IEnumerable<int> offset)
		- public void MarkSequenceBegin(IEnumerable<int> offset, NDShape sectionShape)
	- class Value
	    - public int MaskedCount { get; }
		- public static Value CreateBatch<T>(int dimension, IEnumerable<int> batch, DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device,  bool readOnly = false)
		- public static Value Create<T>(int dimension, IEnumerable<IEnumerable<int>> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)

- The following C# API changes might require changes in applications using them, mainly due to change from concrete class type to interface type.
	- class Function
		- public IList<Variable> Arguments { get; }
		- public IList<Variable> Outputs { get; }
	- class Variable
		- public IList<Axis> DynamicAxes { get; }
	- class Value
		- public static Value CreateBatch<T>(NDShape sampleShape, IEnumerable<T> batch, DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateSequence<T>(NDShape sampleShape,  IEnumerable<T> sequence,  DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateSequence<T>(NDShape sampleShape, IEnumerable<T> sequence,  bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
		- public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, IEnumerable<bool> sequenceStartFlags,DeviceDescriptor device,  bool readOnly = false)
		- public static Value Create<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device,bool readOnly = false)
		- public static Value Create<T>(NDShape sampleShape, IEnumerable<IEnumerable<int>> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
		- public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, DeviceDescriptor device, bool readOnly = false)
		- public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)

- The following C# API will be deprecated soon. Please use the recommend APIs in your application.
	- class Value
		- public void CopyVariableValueTo<T>(Variable outputVariable, List<List<T>> sequences). Instead, please use `public IList<IList<T>> GetDenseData<T>(Variable outputVariable)`.
        - public void CopyVariableValueTo(Variable outputVariable, List<List<uint>> sequences). Instead, please use `public IList<IList<int>> GetOneHotData(Variable outputVariable)`.

The updated APIs are described in [CNTK Library C#/.NET Managed API](../CNTK-Library-Managed-API.md).
