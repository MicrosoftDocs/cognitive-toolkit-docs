---
title:   CNTK_2_0_Beta_6_Release_Notes
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   12/14/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK_2_0_Beta_6_Release_Notes

This is a summary of new features delivered with the Beta 6 release of CNTK V.2.0.

## Microsoft Cognitive Toolkit and NVIDIA CUDA 8

This release adds support of CUDA 8 on Linux (support on Windows was introduced in Beta 5). Binary packages for both Linux and Windows are built using NVIDIA CUDA 8.0. If you are a developer and building CNTK on your own system you can still continue using CUDA 7.5. However note, that this will change soon. See more details [here](../CNTK-move-to-Cuda8.md).

## Python 3.5 support (Linux)

**Linux** version of CNTK is now shipped with the support of Python 3.5. (Support for Windows version will be added soon). To enable Python 3.5 for your CNTK environment you need to pass the following parameter to the installation script:
```
./install-cntk.sh --py-version 35
```
If the script is called without any parameters (or with the parameter ```--py-version 34```) then Python 3.4 will be enabled.

See the full installation instructions for [binary package on Linux here](../Setup-Linux-Binary-Script.md).

## Support for training on one-hot and sparse arrays via NumPy
 
Previously sparse support was provided only in case of using the high-speed readers. Starting from this release it is also possible to provide data via one-hot vectors and CSR matrices.
 
One-hot vectors can be provided by calling one_hot(batch) on the minibatch of index data.

For sparse arrays [scipy.sparse’s csr_matrix](http://docs.scipy.org/doc/scipy/reference/generated/scipy.sparse.csr_matrix.html) is supported. Any other format supported by scipy (e.g. CSC, DOK, or LIL) can be converted to CSR format via their .tocsr() function. 

This is a small example of one_hot:
```
>>> import numpy as np
>>> import cntk as C
>>> num_classes = 6
>>> sparse_indices = [[1,5],[4]]
>>> i0 = C.input_variable(shape=num_classes, is_sparse=True)
>>> z = C.times(i0, np.eye(num_classes))
>>> value = C.one_hot(sparse_indices, num_classes)
>>> z.eval({i0: value})
[array([[ 0.,  1.,  0.,  0.,  0.,  0.],
   [ 0.,  0.,  0.,  0.,  0.,  1.]], dtype=float32), array([[ 0.,  0.,  0.,  0.,  1.,  0.]], dtype=float32)]
```

## New Examples and Tutorials

We have prepared the following Examples and Tutorials:

* [Video action recognition](https://github.com/Microsoft/CNTK/tree/v2.0.beta6.0/Examples/Video/GettingStarted)
* [Sequence-to-Sequence (new tutorial)](https://github.com/Microsoft/CNTK/blob/v2.0.beta6.0/Tutorials/CNTK_204_Sequence_To_Sequence.ipynb)
* [Finance Timeseries Basic with Pandas / Numpy](https://github.com/Microsoft/CNTK/blob/v2.0.beta6.0/Tutorials/CNTK_104_Finance_Timeseries_Basic_with_Pandas_Numpy.ipynb)
* [Build Neural Character Language Models with CNTK](https://github.com/Microsoft/CNTK/tree/v2.0.beta6.0/Examples/Text/CharacterLM/README.md)


## Backwards compatibility change and Stability Improvements

We have introduced the following changes to maintain backwards compatibility:

### Renaming of cntk.graph.find_nodes_by_name() to find_all_with_name()
```cntk.graph.find_nodes_by_name()``` was renamed to ```find_all_with_name()```. In addition, there is now ```find_by_name(name)``` method, which returns a single function or throws an error if there was not found exactly one match of that name. Both methods are also available directly from the function object, e.g.
```
input1 = z.find_by_name('input1')
```

### Enabling save_model() as a method of every CNTK function
```save_model()``` is now a method of every CNTK function. So instead of calling
```
save_model(z, filename)
```
you now need to execute
```
z.save_model(filename)
```
```load_model()``` works the same way as before but you import it from ```cntk.ops.functions```. See more in the [article](../How-do-I-Evaluate-models-in-Python.md#evaluate-a-saved-convolutional-network).

We also continue fine tuning new features and fixing different bugs - thank you once again for the constant feedback. You are not required to adopt your code or models to take an advantage of these improvements.
