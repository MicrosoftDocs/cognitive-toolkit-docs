---
title:   CNTK_2_0_RC_2_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   05/24/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

## CNTK v.2.0 RC 3 Release Notes

With the Release Candidate 3 we ship the last preview before the Microsoft Cognitive Toolkit V2 final release. 

### CNTK Core and Python API

* Several APIs that were previously deprecated, have now been removed. See the table listing removed API and their replacements at the end of these Release Notes.
* Support for static axes with unspecified dimensionality in input variable shapes. The actual dimensionality is determined when the first minibatch data is bound for computation by the Function.
* Support for free (dynamic) static axes in input variable shapes that infer the actual dimensionality value from bound data, and can change across different evaluations of the Function graph (Note: Currently some Functions like convolution and pooling do not support inputs with free static axes; this will be addressed soon).
* New `to_sequence` operator converting non-sequence data to sequence data.
* New `sequence.unpack` operator converting non-sequence data to sequence.
* Convolution in 1D is now supported. 
* Added support for UDF serialization (available both in Python and native in C++).
* `Base64ImageDeserializer` is now available in Python
* [**Crosstalk** is now in preview](https://github.com/Microsoft/CNTK/tree/master/bindings/python/cntk/contrib/crosstalk). This is a tool for comparing and converting tensors/parameters between different DL platforms. Currently only have limited support for CNTK and Tensorflow (v.0.12 or greater).
* Introducing [**RNN Conversion tool**](https://github.com/Microsoft/CNTK/blob/master/bindings/python/cntk/misc/optimized_rnnstack_converter.py) to change `optimized_rnnstack` node to GEMM based RNNs, so the model can be used on CPU.

### CNTK Java API

Java API is added to support model evaluation in Java on Windows and Linux. Note, that the API is still *experimental* and subject to change. Feedback from community is greatly appreciated. See the details at the following pages: [Java API on Windows](../CNTK-Library-Evaluation-on-Windows.md#using-java), and [Java API on Linux](../CNTK-Library-Evaluation-on-Linux.md#using-java).

### cuDNN v.6.0 support when building CNTK from source code

CNTK v.2.0 RC 3 binary distribution and Docker Hub images are built using cuDNN 5.1. However, it is now possible to build CNTK from source code using [NVIDIA cuDNN Library](https://developer.nvidia.com/cudnn) v.6.0. This is what is needed to enable that:
* **Linux.** Pass the path to cuDNN via the parameter in `configure' script. Example:
```
./configure --with-cudnn=/path/to/cudnn6
```
* **Windows.** Set environment variable `%CUDNN_PATH%` to to the cuDNN installation path, e.g. `c:\path\to\cudnn6\cuda`

### Bug fixes, improvements, and recommendations

* RNG users (`Dropout`, `RandomSample`, `RandomSampleWithFrequency` and Parameter initialization) need to be sure to honor `set_fixed_random_seed` setting.
* Added `initial seed` reader configuration option.
* Minor usability improvements in device selection/locking.
* Preventing an infinite loop in random parameter initialization (an exception is raised if `initializer` scale is <= 0).
* Made `sigmoid` and `LogSum` numerically stable.
* Fixed backprop for `LogSum`.
* Added support for `input` variables without any dynamic axes.
* Added support for executing `Function.eval` before `Function.grad`.
* Several improvements and bug fixes for `UserFunction`.
* Several sparse data handling fixes.


### CNTK NuGet package

A new set of NuGet Packages is provided with this Release. 

**IMPORTANT!** For Visual Studio: In the *Manage NuGet Packages* window change the default option *Stable Only* to *Include Prerelease*. Otherwise the release candidate package of CNTK will not be visible. This Package version is ```2.0.0-rc3```.

### Removed deprecated API and their replacements

In [CNTK 2.0 RC 3](https://github.com/Microsoft/CNTK/releases/tag/v2.0.rc3) we have removed the API mentioned in the table below. All API were previously declared as deprecated. Also see what API are replacing the deprecated and removed ones.

| Removed API (was previously deprecated) | Replacement API |
|:---------|:------------|
|`Axis.end_static_axis()`|`Axis.new_leading_axis()`<sup name="a1">[1](#footnote1)</sup>|
|Module `blocks`|Module `layers`<sup name="a2">[2](#footnote2)</sup>|
|`MinibatchData.value`|`asarray()`, `as_sequences()`, or `data`<sup name="a3">[3](#footnote3)</sup>|
|`MinibatchSource` constructor parameters:<br>a) `randomization_window`, `sample_based_randomization_window`<br>b) `epoch_size`|Replacements for removed `MinibatchSource` constructor parameters:<br>a) `randomization_window_in_chunks` or `randomization_window_in_samples`<br>b) `max_samples` or `max_sweeps`|
|`cntk.future_value`<br>`cntk.ops.future_value`<br>`cntk.past_value`<br>`cntk.ops.past_value`|`cntk.sequence.future_value`<br>`cntk.ops.sequence.future_value`<br>`cntk.sequence.past_value`<br>`cntk.ops.sequence.past_value`|
|`cntk.placeholder_variable`<br>`cntk.ops.placeholder_variable`|`cntk.placeholder`<br>`cntk.ops.placeholder`|
|`cntk.set_default_device`<br>`cntk.device.set_default_device`|`cntk.try_set_default_device`<br>`cntk.device.try_set_default_device`|
|`cntk.layers.LayerStack`|`cntk.layers.For`|
|`cntk.logging.graph.output_function_graph`|`cntk.logging.graph.plot`|
|`Function.save_model`<br>`Function.restore_model`<br>`cntk.save_model`|`Function.save`<br>`Function.restore`<br>`Function.save`|

<b name="footnote1">1</b> Creates an `Axis` object representing a new leading static axis. [↩](#a1)<br>
<b name="footnote2">2</b> `blocks` module should not be imported separately anymore. Only import `layers` which includes `blocks`. [↩](#a2)<br>
<b name="footnote3">3</b> Use `asarray()` or `as_sequences()` to get the data in numpy/scipy representation or `data` to get the opaque underlying `Value` object representation. [↩](#a3)
