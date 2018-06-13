---
title:   CNTK_2_0_RC_1_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/03/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_RC_1_Release_Notes

With Release Candidate 1 the Microsoft Cognitive Toolkit enters the final set of enhancements before release of the production version of CNTK 2.0. We expect no breaking changes before the release of the production version, but we are eager to hear your feedback!

## Highlights of CNTK 2.0 Release Candidate 1

The release candidate contains [all changes and improvements introduced in CNTK 2.0 during beta phase](../CNTK-2.0-Beta-Highlights.md).

## Breaking changes

This release contains the following **breaking changes**:

* New file names of CNTK Shared Libraries. Read more [here](../CNTK-Shared-Libraries-Naming-Format.md).
  * This is important for developers and other users who use explicit CNTK Shared Library file names in their solutions.
  * If you are using CNTK NuGet packages for C++ or C# no action is required (beside updating to the latest NuGet package)
* There is a set of breaking changes in Python and C# API. See the correspondent sections below.

## CNTK Core: new and improved features

* Improved performance and memory footprint in CSC/CSR sparse matrix operations.
* Caffe-converted pretrained models on image classification including AlexNet, ResNet, VGG and BN-Inception. 
* Multiple-axis slicing. 
* Improvements in the device selection API (specifying excluded devices, querying device properties, locking device exclusively).
* Limit the number of sweeps for training.

## CNTK Python API

### New feature

We enabled support for model debugging in Python (similar to gdb/pdb) by wrapping the model with `debug_model()` [Read more here](https://cntk.ai/pythondocs/cntk.debugging.html#module-cntk.debugging.debug).

### Breaking changes

This release contains the following **breaking changes in CNTK Python API**:

* Automatic conversion to NumPy array via `np.asarray(cntk_obj)` is being replaced by `asarray()`/`as_sequences()` methods on those objects
  * Reasons:
    * `np.assarray()` always returns NumPy arrays, so no sequences could be supported
    * `np.assarray()` only supports dense arrays
    * `np.assarray()` does not allow to throw exceptions from within the array interface
  * Solution:
    * `asarray()`  returns dense or sparse (instance of  `scipy.csr_matrix`) versions of the underlying data
      * supported by  `Constant`,  `Parameter`,  `NDArrayView`,  `Value`, and  `MinibatchData`
    * `as_sequences(var)`  returns a Python list of sequences whose elements are NumPy arrays (according to var's dynamic and static axes)
      * supported by `Value` and `MinibatchData`  
* `forward()` and `eval()` behave more consistent now
  * Before: when the returned result did had sequences all of same length, they would be returned as a single NumPy array.
  * Now: independent of the sequences, the result will always be a list if the output has a sequence axis. This will impact every usage that has the sequence axis in the output variable (e.g. if you just used `input_variable()` with standard settings and did not get rid of the sequence axes before the final output node).
* `MinibatchData.value` is deprecated now because of performance issues
  * Replacement: use `MinibatchData.data`, which returns a `Value` instance that does not need to be converted before passing it to the Trainer and thus is much faster.
* Deprecated parameters were removed from `training_session`. The progress printer should be now passed directly to the trainer, not to the training session. To configure different aspects of the training session, please use configuration objects. In the new version CNTK supports:
  * checkpoint configuration
  * cross validation configuration
  * test configuration

## CNTK C#/.NET Managed API

### New and improved features

* Support of CSC Sparse input.
* More flexible interface types in API to provide more freedom for developers. 
* Improved CLS compliance to better support other .NET languages.
* Performance optimizations.
* Multiple bug fixes, including memory safety and concurrent evaluations.

The updated APIs are described [here](../CNTK-Library-Managed-API.md).

### Breaking changes

This release contains the following **breaking changes in CNTK C# API**:

* The following C# APIs have been moved to new classes:
  * `CNTKLib.Alias()` -> `Function.Alias()`
  * `CNTKLib.AsComposite()` -> `Function.AsComposite()`
  * `CNTKLib.Combine()` -> `Function.Combine()`
  * `CNTKLib.SetMaxNumCPUThreads()` -> `Utils.SetMaxNumCPUThreads()`
  * `CNTKLib.GetMaxNumCPUThreads()` -> `Utils.GetMaxNumCPUThreads()`
* We have the following two groups of API changes due to signature simplification and certain type changes. These changes may require adaptations in dependent applications.

#### C# API changes due to type change from `uint` to `int`

* Class `NDShape` 
  ```
  public NDShape(int numAxes, int dimension)
  public NDShape(int numAxes)
  public int Rank { get; }  
  public IList<int> Dimensions { get; }
  public int TotalSize { get; }
  public int this[int key] { get; }  
  public NDShape SubShape(int beginAxisId, int endAxisId)    
  public NDShape SubShape(int beginAxisId)    
  public static NDShape CreateNDShape(IEnumerable<int> dimensions)
  ```  
* Class `DeviceDescriptor`
  ```
  public int Id { get; }
  public static DeviceDescriptor GPUDevice(int deviceId)  
  ``` 
* Class `NDArrayView`
  ```
  public NDArrayView(NDShape viewShape, float[] dataBuffer, DeviceDescriptor device, bool readOnly = false)
  public NDArrayView(NDShape viewShape, double[] dataBuffer, DeviceDescriptor device, bool readOnly = false) 
  public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, float[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
  public NDArrayView(NDShape viewShape, int[] colStarts, int[] rowIndices, double[] nonZeroValues, DeviceDescriptor device, bool readOnly = false)
  ```
* Class `NDMask`
  ```
  public int MaskedCount { get; }
  public void InvalidateSection(IEnumerable<int> sectionOffset, NDShape sectionShape)
  public void MarkSequenceBegin(IEnumerable<int> offset)
  public void MarkSequenceBegin(IEnumerable<int> offset, NDShape sectionShape) 
  ``` 
* Class `Value`
  ```
  public int MaskedCount { get; }
  public static Value CreateBatch<T>(int dimension, IEnumerable<int> batch, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateSequence<T>(int dimension, IEnumerable<int> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateBatchOfSequences<T>(int dimension, IEnumerable<IEnumerable<int>> batchOfSequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device,  bool readOnly = false)
  public static Value Create<T>(int dimension, IEnumerable<IEnumerable<int>> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
  ```
  #### C# API changes due to type change from concrete class type to interface type

* Class `Function`
  ```
  public IList<Variable> Arguments { get; } 
  public IList<Variable> Outputs { get; }
  ```
* Class `Variable`
  ```
  public IList<Axis> DynamicAxes { get; }
  ``` 
* Class `Value`
  ```
  public static Value CreateBatch<T>(NDShape sampleShape, IEnumerable<T> batch, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateSequence<T>(NDShape sampleShape, IEnumerable<T> sequence, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateSequence<T>(NDShape sampleShape, IEnumerable<T> sequence, bool sequenceStartFlag, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, DeviceDescriptor device, bool readOnly = false)
  public static Value CreateBatchOfSequences<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> batchOfSequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device,  bool readOnly = false)
  public static Value Create<T>(NDShape sampleShape, IEnumerable<IEnumerable<T>> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
  public static Value Create<T>(NDShape sampleShape, IEnumerable<IEnumerable<int>> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
  public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, DeviceDescriptor device, bool readOnly = false)
  public static Value Create(NDShape sampleShape, IEnumerable<NDArrayView> sequences, IEnumerable<bool> sequenceStartFlags, DeviceDescriptor device, bool readOnly = false)
  ```

## CNTK NuGet package

A new set of NuGet Packages is provided with this Release. 

**IMPORTANT!** In Visual Studio *Manage NuGet Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the packages will not be visible. The Package version should be ```2.0.0-rc1```.
