---
title:   CNTK_2_4_Release_Notes
author:    manikj
ms.author:   manikj
ms.date:   01/31/2018
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.prod: cntk
ms.devlang:   NA
---

# CNTK v2.4 Release Notes

## Highlights of this release
- Moved to CUDA9, cuDNN 7 and Visual Studio 2017.
- Removed Python 3.4 support.
- Added Volta GPU and FP16 support.
- Better ONNX support.
- CPU perf improvement.
- More OPs.

## OPs
- ``top_k`` operation: in the forward pass it computes the top (largest) k values and corresponding indices along the specified axis. In the backward pass the gradient is scattered to the top k elements (an element not in the top k gets a zero gradient).
- ``gather`` operation now supports an axis argument
- ``squeeze`` and ``expand_dims`` operations for easily removing and adding singleton axes
- ``zeros_like`` and ``ones_like`` operations. In many situations you can just rely on CNTK correctly broadcasting a simple 0 or 1 but sometimes you need the actual tensor.
- ``depth_to_space``: Rearranges elements in the input tensor from the depth dimension into spatial blocks. Typical use of this operation is for implementing sub-pixel convolution for some image super-resolution models.
- ``space_to_depth``: Rearranges elements in the input tensor from the spatial dimensions to the depth dimension. It is largely the inverse of DepthToSpace.
- ``sum`` operation: Create a new Function instance that computes element-wise sum of input tensors.
- ``softsign`` operation: Create a new Function instance that computes the element-wise softsign of a input tensor.
- ``asinh`` operation: Create a new Function instance that computes the element-wise asinh of a input tensor.
- ``log_softmax`` operation: Create a new Function instance that computes the logsoftmax normalized values of a input tensor.
- ``hard_sigmoid`` operation: Create a new Function instance that computes the hard_sigmoid normalized values of a input tensor.
- ``element_and``, ``element_not``, ``element_or``, ``element_xor`` element-wise logic operations
- ``reduce_l1`` operation: Computes the L1 norm of the input tensor's element along the provided axes.
- ``reduce_l2`` operation: Computes the L2 norm of the input tensor's element along the provided axes..
- ``reduce_sum_square`` operation: Computes the sum square of the input tensor's element along the provided axes.
- ``image_scaler`` operation: Alteration of image by scaling its individual values.

## ONNX
- There have been several improvements to ONNX support in CNTK.
- Updates
  - Updated ONNX ``Reshape`` op to handle ``InferredDimension``.
  - Adding ``producer_name`` and ``producer_version`` fields to ONNX models.
  - Handling the case when neither ``auto_pad`` nor ``pads`` atrribute is specified in ONNX ``Conv`` op.
- Bug fixes
  - Fixed bug in ONNX ``Pooling`` op serialization
  - Bug fix to create ONNX ``InputVariable`` with only one batch axis.
  - Bug fixes and updates to implementation of ONNX ``Transpose`` op to match updated spec.
  - Bug fixes and updates to implementation of ONNX ``Conv``, ``ConvTranspose``, and ``Pooling`` ops to match updated spec.

## Operators
- Group convolution
  - Fixed bug in group convolution. Output of CNTK ``Convolution`` op will change for groups > 1. More optimized implementation of group convolution is expected in the next release.
  - Better error reporting for group convolution in ``Convolution`` layer.

## Halide Binary Convolution
- The CNTK build can now use optional [Halide](http://halide-lang.org/) libraries to build ``Cntk.BinaryConvolution.so/dll`` library that can be used with the ``netopt`` module. The library contains optimized binary convolution operators that perform better than the python based binarized convolution operators. To enable Halide in the build, please download [Halide release](https://github.com/halide/Halide/releases) and set ``HALIDE_PATH`` environment varibale before starting a build. In Linux, you can use ``./configure --with-halide[=directory]`` to enable it. For more information on how to use this feature, please refer to [How_to_use_network_optimization](https://github.com/Microsoft/CNTK/blob/master/Manual/Manual_How_to_use_network_optimizations.ipynb).
