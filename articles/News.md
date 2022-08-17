---
title:   News
author:    alexeyo26
ms.author:   alexeyo
ms.date:   07/31/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# News

Dear community, 

With our ongoing contributions to ONNX and the ONNX Runtime, we have made it easier to interoperate within the AI framework ecosystem and to access high performance, cross-platform inferencing capabilities for both traditional ML models and deep neural networks. Over the last few years we have been privileged to develop such key open-source machine learning projects, including the Microsoft Cognitive Toolkit, which has enabled its users to leverage industry-wide advancements in deep learning at scale.

Today’s 2.7 release will be the last main release of CNTK. We may have some subsequent minor releases for bug fixes, but these will be evaluated on a case-by-case basis. There are no plans for new feature development post this release. 

The CNTK 2.7 release has full support for ONNX 1.4.1, and we encourage those seeking to operationalize their CNTK models to take advantage of ONNX and the ONNX Runtime. Moving forward, users can continue to leverage evolving ONNX innovations via the number of frameworks that support it. For example, users can natively export ONNX models from PyTorch or convert TensorFlow models to ONNX with the TensorFlow-ONNX converter. 

We are incredibly grateful for all the support we have received from contributors and users over the years since the initial open-source release of CNTK. CNTK has enabled both Microsoft teams and external users to execute complex and large-scale workloads in all manner of deep learning applications, such as historical breakthroughs in speech recognition achieved by Microsoft Speech researchers, the originators of the framework. 

As ONNX is increasingly employed in serving models used across Microsoft products such as Bing and Office, we are dedicated to synthesizing innovations from research with the rigorous demands of production to progress the ecosystem forward. 

Above all, our goal is to make innovations in deep learning across the software and hardware stacks as open and accessible as possible. We will be working hard to bring both the existing strengths of CNTK and new state-of-the-art research into other open-source projects to truly broaden the reach of such technologies. 

With gratitude, 

-- The CNTK Team 

***2019-03-29.*** CNTK 2.7.0
## Highlights of this release
* Moved to CUDA 10 for both Windows and Linux.
* Support advance RNN loop in ONNX export.
* Export larger than 2GB models in ONNX format.
* Support FP16 in Brain Script train action.

## CNTK support for CUDA 10

### CNTK now supports CUDA 10. This requires an update to build environment to Visual Studio 2017 v15.9 for Windows.

To setup build and runtime environment on Windows:
* Install [Visual Studio 2017](https://www.visualstudio.com/downloads/). Note: going forward for CUDA 10 and beyond, it is no longer required to install and run with the specific VC Tools version 14.11.
* Install [Nvidia CUDA 10](https://developer.nvidia.com/cuda-downloads?target_os=Windows&target_arch=x86_64)
* From PowerShell, run:
    [DevInstall.ps1](https://github.com/microsoft/CNTK/blob/release/latest/Tools/devInstall/Windows/DevInstall.ps1)
* Start Visual Studio 2017 and open [CNTK.sln](https://github.com/microsoft/CNTK/blob/release/latest/CNTK.sln)

To setup build and runtime environment on Linux using docker, please build Unbuntu 16.04 docker image using Dockerfiles [here](https://github.com/Microsoft/CNTK/tree/release/latest/Tools/docker). For other Linux systems, please refer to the Dockerfiles to setup dependent libraries for CNTK.

## Support advance RNN loop in ONNX export
CNTK models with recursive loops can be exported to ONNX models with scan ops.

## Export larger than 2GB models in ONNX format
To export models larger than 2GB in ONNX format, use cntk.Function API:
save(self, filename, format=ModelFormat.CNTKv2, use_external_files_to_store_parameters=False)
with 'format' set to ModelFormat.ONNX and use_external_files_to_store_parameters set to True.
In this case, model parameters are saved in external files. Exported models shall be used with external parameter files when doing model evaluation with onnxruntime.

***2018-11-26.***  
[Netron](https://github.com/lutzroeder/netron) now supports visualizing CNTK v1 and CNTK v2 `.model` files.

<img src=https://cntk.ai/Images/netron/netron-cntk-dark-1.png alt="NetronCNTKDark1" width="300"> <img src=https://cntk.ai/Images/netron/netron-cntk-light-1.png alt="NetronCNTKLight1" width="300">

##September 2018
***2018-09-17.* CNTK 2.6.0**
Highlights:
* .Net Support
* Efficient group convolution.
* Improvement of sequential convolution.
* More operators and improvement of existing ones.
* ONNX feature update to support ONNX 1.2.2.
* More ops supported by ONNX converter.
* Bug fixes.

See more in the [Release Notes](/cognitive-toolkit/ReleaseNotes/CNTK_2_6_Release_Notes).
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## April 2018
***2018-04-16.* CNTK 2.5.1**

Highlights:
* Repack of CNTK 2.5 with third party libraries included in the bundle.

## March 2018
***2018-03-15.* CNTK 2.5**

Highlights:
* Change profiler details output format.
* Enable per-node timing.
* CPU inference performance improvements using MKL.
* 1BitSGD incorporated into CNTK.
* New loss function.
* Distributed Training with Mulitple Learners.
* More OPs.
* Bug fixes and updates for ONNX.

See more in the [Release Notes](/cognitive-toolkit/ReleaseNotes/CNTK_2_5_Release_Notes).
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## January 2018

***2018-01-31.* CNTK 2.4**

Highlights:
* Moved to CUDA9, cuDNN 7 and Visual Studio 2017.
* Removed Python 3.4 support.
* Added Volta GPU and FP16 support.
* Better ONNX support.
* CPU perf improvement.
* More OPs.

See more in the [Release Notes](/cognitive-toolkit/ReleaseNotes/CNTK_2_4_Release_Notes).
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).


***2018-01-22.*** CNTK support for CUDA 9

CNTK now supports CUDA 9/cuDNN 7. This requires an update to build environment to Ubuntu 16/GCC 5 for Linux, and Visual Studio 2017/VCTools 14.11 for Windows. With CUDA 9, CNTK also added a preview for 16-bit floating point (a.k.a FP16) computation.

Please check out the example of FP16 in ResNet50 [here](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Image/Classification/ResNet/Python/TrainResNet_ImageNet_Distributed.py)

Notes on FP16 preview:
* FP16 implementation on CPU is not optimized, and it's not supposed to be used in CPU inference directly. User needs to convert the model to 32-bit floating point before running on CPU.
* Loss/Criterion for FP16 training needs to be 32bit for accumulation without overflow, using cast function. Please check the example above.
* Readers do not have FP16 output unless using numpy to feed data, cast from FP32 to FP16 is needed. Please check the example above.
* FP16 gradient aggregation is currently only implemented on GPU using NCCL2. Distributed training with FP16 with MPI is not supported.
* FP16 math is a subset of current FP32 implementation. Some model may get Feature Not Implemented exception using FP16.
* FP16 is currently not supported in BrainScript. Please use Python for FP16.

To setup build and runtime environment on Windows:
* Install [Visual Studio 2017](https://www.visualstudio.com/downloads/) with following workloads and components. From command line (use Community version installer as example):
    vs_community.exe --add Microsoft.VisualStudio.Workload.NativeDesktop --add Microsoft.VisualStudio.Workload.ManagedDesktop --add Microsoft.VisualStudio.Workload.Universal --add Microsoft.Component.PythonTools --add Microsoft.VisualStudio.Component.VC.Tools.14.11
* Install [NVidia CUDA 9](https://developer.nvidia.com/cuda-90-download-archive?target_os=Windows&target_arch=x86_64)
* From PowerShell, run:
    [DevInstall.ps1](https://github.com/Microsoft/CNTK/tree/release/latest/Tools/devInstall/Windows/DevInstall.ps1)
* Start VCTools 14.11 command line, run:
    cmd /k "%VS2017INSTALLDIR%\VC\Auxiliary\Build\vcvarsall.bat" x64 --vcvars_ver=14.11
* Open [CNTK.sln](https://github.com/Microsoft/CNTK/tree/release/latest/CNTK.sln) from the VCTools 14.11 command line. Note that starting CNTK.sln other than VCTools 14.11 command line, would causes CUDA 9 [build error](https://developercommunity.visualstudio.com/content/problem/163758/vs-2017-155-doesnt-support-cuda-9.html).

To setup build and runtime environment on Linux using docker, please build Unbuntu 16.04 docker image using Dockerfiles [here](https://github.com/Microsoft/CNTK/tree/release/latest/Tools/docker). For other Linux systems, please refer to the Dockerfiles to setup dependent libraries for CNTK.

## November 2017
***2017-11-22.* CNTK 2.3**
Release of Cognitive Toolkit v.2.3.

Highlights:
* Better ONNX support.
* Switched to NCCL2 for better performance in distributed training.
* Improved C# API.
* OpenCV is not required to install CNTK, it is only required for Tensorboard Image feature and image reader.
* Various performance improvement.
* Added Network Optimization API.
* Faster Adadelta for sparse.

See more in the [Release Notes](/cognitive-toolkit/ReleaseNotes/CNTK_2_3_Release_Notes).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## September 2017
***2017-09-24.* CNTK R-binding**  
CNTK R-binding is released at: https://github.com/Microsoft/CNTK-R. 

***2017-09-15.* CNTK 2.2**  
Release of Cognitive Toolkit v.2.2.

Highlights:
* Documentation
  - Add HTML version of tutorials and manuals so that they can be searchable
  - Updated evaluation documents
* System
  - 16bit support for training on Volta GPU (limited functionality)    
  - Support for NCCL 2
  - CNTK learner interface update
  - C#/.NET API that enables people to build and train networks
  - R-binding for CNTK
* Examples
  - Object Detection with Fast R-CNN and Faster R-CNN
  - New C++ Eval Examples
  - New C# Eval examples
* Operations
  - Noise contrastive estimation node
  - Improved AttentionModel
  - Aggregation on sparse gradient for embedded layer
  - Reduced rank for convolution in C++ to enable convolution on 1D data
  - Dilated convolution (GPU only)
  - Free static axes support for convolution
  - Deterministic Pooling
  - Add Crop Node to Python API
* Performance
  - Intel MKL update to improve inference speed on CPU by around 2x on AlexNet
* Keras and Tensorboard
  - Multi-GPU support for Keras on CNTK
  - Tensorboard image support for CNTK

See more in the [Release Notes](/cognitive-toolkit/ReleaseNotes/CNTK_2_2_Release_Notes).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## July 2017
***2017-07-31.* CNTK 2.1**  
Release of Cognitive Toolkit v.2.1.

Highlights:
* cuDNN 6.0 integration
* Support of Universal Windows Platform (UWP)
* Improvements in backend for Keras
* Performance improvements
* New manuals, tutorials and examples
* Multiple bug fixes

See more in the [Release Notes](/cognitive-toolkit/ReleaseNotes/CNTK_2_1_Release_Notes).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## June 2017
***2017-06-26.* A great class for getting started with both Deep Learning and CNTK, Deep Learning Explained is now available on edX.**

***2017-06-01.* CNTK 2.0**  
The first production release of Cognitive Toolkit 2.0.

Highlights:
* CNTK backend for Keras.
* Extremely fast binary convolution with Halide.
* Java API.
* A new set of NuGet Packages.
* Multiple bug fixes.

See more in the [Release Notes](/cognitive-toolkit/ReleaseNotes/CNTK_2_0_Release_Notes).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## May 2017
***2017-05-24.* CNTK 2.0 Release Candidate 3**  
Release Candidate 3 is the final preview of Cognitive Toolkit v.2.0.

Highlights:
* API that were previously declared deprecated are now ***removed***. See details in [release notes](./ReleaseNotes/CNTK_2_0_RC_3_Release_Notes.md). 
* Introduction of CNTK Java API in experimental mode. See details in [release notes](./ReleaseNotes/CNTK_2_0_RC_3_Release_Notes.md).
* New operators like `to_sequence` and `sequence.unpack`.
* Support of convolution in 1D. 
* Support of UDF serialization (available both in Python and native in C++).
* New tools ([Crosstalk](https://github.com/Microsoft/CNTK/tree/release/latest/bindings/python/cntk/contrib/crosstalk) and [RNN Conversion](https://github.com/Microsoft/CNTK/tree/release/latest/bindings/python/cntk/misc/optimized_rnnstack_converter.py)).
* Support of NVIDIA cuDNN v.6.0 when CNTK is built by the user from source code.
* A new set of NuGet Packages.
* Multiple bug fixes.

See more in the [Release Notes](./ReleaseNotes/CNTK_2_0_RC_3_Release_Notes.md).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## April 2017
***2017-04-21.* CNTK 2.0 Release Candidate 2**  
With Release Candidate 2 we reacted to customer feedback and improved/added features, functionality, and performance.

Highlights:
* New operators like `pow`, `sequence.reduce_max`, `sequence.softmax`.
* New feature for Linux source builds (GPU Direct RDMA support in distributed gradients aggregation, NCCL support for Python in V2 gradients aggregation).
* Support for Python 3.6 for source and binary installation; see [here](./Setup-CNTK-on-your-machine.md).
* `UserMinibatchSource` to write custom minibatch sources; see [here](https://cntk.ai/pythondocs/extend.html#user-defined-minibatch-sources).
* New C# APIs: `class NDArrayView` and methods, `SetMaxNumCPUThreads()`, `GetMaxNumCPUThreads()`, `SetTraceLevel()`, `GetTraceLevel()`
* A new set of NuGet Packages is provided with this Release. 

The [release notes](./ReleaseNotes/CNTK_2_0_RC_2_Release_Notes.md) contain an overview. 
Get the release from the [CNTK Releases Page](https://github.com/Microsoft/CNTK/releases).

## March 2017

***2017-03-31.* V 2.0 Release Candidate 1**  
With Release Candidate 1 the Microsoft Cognitive Toolkit enters the final set of enhancements before release of the production version of CNTK v.2.0. We expect no breaking changes before the release of the production version, but we are eager to hear your feedback!

Highlights:
* The release candidate contains [all changes and improvements introduced in CNTK 2.0 during beta phase](./CNTK-2.0-Beta-Highlights.md).
* Enables Caffe-converted pretrained models on image classification including AlexNet, ResNet, VGG and BN-Inception. 
* Slice now supports multiple-axis slicing.
* Improves performance and memory footprint
* Improvements in the [device selection API](./ReleaseNotes/CNTK_2_0_RC_1_Release_Notes.md).
* New Python [model debugging functions](https://cntk.ai/pythondocs/cntk.debugging.html#module-cntk.debugging.debug).
* Improvements in Python and C# API. See the [release notes](./ReleaseNotes/CNTK_2_0_RC_1_Release_Notes.md) for detailed description.
* New file names for [CNTK libraries and DLLs](./CNTK-Shared-Libraries-Naming-Format.md).
* Various bug fixes.

The [release notes](./ReleaseNotes/CNTK_2_0_RC_1_Release_Notes.md) contain an overview. 
Get the release from the [CNTK Releases Page](https://github.com/Microsoft/CNTK/releases).

***2017-03-24.* Important Note breaking changes**
There are breaking changes in master compared to beta15, click [here](./Breaking-changes-in-Master-compared-to-beta15.md) for more information.

***2017-03-16.* V 2.0 Beta 15 Release available at Docker Hub**  
CNTK V 2.0 Beta 15 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [article](./CNTK-Docker-Containers.md).

***2017-03-15.* V 2.0 Beta 15 Release**  
Highlights of this Release:
*  In addition to pre-existing Python support, added support for TensorBoard output in BrainScript. [Read more here](./Using-TensorBoard-for-Visualization.md).
* Learners can now be implemented in pure Python by means of `UserLearners`. [Read more here](https://cntk.ai/pythondocs/extend.html#user-learners).
* New debugging helpers: `dump_function()`, `dump_signature()`.
* Tensors can be indexed using advanced indexing. E.g. `x[[0,2,3]]` would return a tensor that contains the first, third and fourth element of the first axis.
* Significant updates in the [Layers Library](https://cntk.ai/pythondocs/layerref.html) of Python API. See [Release Notes](./ReleaseNotes/CNTK_2_0_beta_15_Release_Notes.md) for detailed description.
* Updates and [new examples](./CNTK-Eval-Examples.md#examples-for-evaluating-multiple-requests-in-parallel) in C# API.
* Various bug fixes.

See more in the [Release Notes](./ReleaseNotes/CNTK_2_0_beta_15_Release_Notes.md).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## February 2017
***2017-02-28.* V 2.0 Beta 12 Release available at Docker Hub**  
CNTK V 2.0 Beta 12 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [article](./CNTK-Docker-Containers.md).

***2017-02-23.* V 2.0 Beta 12 Release**  
Highlights of this Release:
* New and updated features: new activation functions, support of `Argmax` and `Argmin`, improved performance of `numpy` interop, new functionality of existing operators, and more.
* [CNTK for CPU on Windows can now be installed via `pip install` on Anaconda 3](./Setup-CNTK-on-your-machine.md). Other configurations will be enabled soon.
* HTK deserializers are now exposed in Python. All deserializers are exposed in C++.
* The memory pool implementation of CNTK has been updated with a new global optimization algorithm. Hyper memory compression has been removed.
* New features in C++ API.
* [New Eval examples for RNN models](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs).
* New [CNTK NuGet Packages](./NuGet-Package.md) with CNTK V2 C++ Library.

See more in the [Release Notes](./ReleaseNotes/CNTK_2_0_beta_12_Release_Notes.md).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

***2017-02-13.* V 2.0 Beta 11 Release available at Docker Hub**  
CNTK V 2.0 Beta 11 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [article](./CNTK-Docker-Containers.md).

***2017-02-10.* V 2.0 Beta 11 Release**  
Highlights of this Release:
* New and updated features: reduce_prod, reductions across all axes, denominator sharing, memory improvement, & more...
* New Tutorials and Examples:
  * [Transfer learning example to train custom image classifiers](https://github.com/Microsoft/CNTK/tree/v2.0.beta11.0/Examples/Image/TransferLearning).
* New [CNTK NuGet Packages](./NuGet-Package.md).
  * Note a *breaking change* due to Assembly Strong Name enabling. See [Release Notes](./ReleaseNotes/CNTK_2_0_beta_11_Release_Notes.md).

See more in the [Release Notes](./ReleaseNotes/CNTK_2_0_beta_11_Release_Notes.md).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

***2017-02-08.* V 2.0 Beta 10 Release available at Docker Hub**  
CNTK V 2.0 Beta 10 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [article](./CNTK-Docker-Containers.md).

***2017-02-01.* V 2.0 Beta 10 Release**  
Highlights of this Release:
* New and updated core and Python API features ([Operators with UserFunctions](https://cntk.ai/pythondocs/extend.html), [Tensorboard support](./Using-TensorBoard-for-Visualization.md), [Python API Fast R CNN](./Object-Detection-using-Fast-R-CNN.md)).
* Improved speed of CrossEntropyWithSoftmax and ClassificationError for sparse labels.
* New Tutorials and Examples:
  * A Python version of the deconvolution layer and image auto encoder example was added ([Example **07_Deconvolution** in *Image - Getting Started*](https://github.com/Microsoft/CNTK/tree/v2.0.beta10.0/Examples/Image/GettingStarted)).
  * A Python distributed training example for image classification using AlexNet was added, cf. [here](https://github.com/Microsoft/CNTK/tree/v2.0.beta10.0/Examples/Image/Classification/AlexNet/Python)
  * [Basic implementation of Generative Adversarial Networks (GAN) networks](https://github.com/Microsoft/CNTK/blob/v2.0.beta10.0/Tutorials/CNTK_206_Basic_GAN.ipynb)
  * [Training with Sampled Softmax](https://github.com/Microsoft/CNTK/blob/v2.0.beta10.0/Tutorials/CNTK_207_Training_with_Sampled_Softmax.ipynb)
* New [CNTK NuGet Packages](./NuGet-Package.md).

See more in the [Release Notes](./ReleaseNotes/CNTK_2_0_beta_10_Release_Notes.md).
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

## January 2017
***2017-01-25.* V 2.0 Beta 9 Release available at Docker Hub**  
CNTK V 2.0 Beta 9 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [article](./CNTK-Docker-Containers.md).

***2017-01-25.* 1bit-SGD Code is relocated to GitHub. Submodule configuration update is required for affected users**  
This news is related to users who are working with CNTK code base. If you use Binary or Docker Runtime Images installation you may ignore it.

***2017-01-20.* V 2.0 Beta 9 Release**  
Highlights of this Release:
* Default Python version is now 3.5 (relates to default parameters in client installations as well as [Runtime Images at Docker Hub](./CNTK-Docker-Containers.md)).
* New and updated core and Python API features.
* New Tutorials and Examples:
  * Deconvolution layer and image auto encoder example using deconvolution and unpooling ([Example **07_Deconvolution** in *Image - Getting Started*](https://github.com/Microsoft/CNTK/tree/v2.0.beta9.0/Examples/Image/GettingStarted)).
  * [Basic autoencoder with MNIST data](https://github.com/Microsoft/CNTK/blob/v2.0.beta9.0/Tutorials/CNTK_105_Basic_Autoencoder_for_Dimensionality_Reduction.ipynb).
  * [LSTM Timeseries with Simulated Data (Part A)](https://github.com/Microsoft/CNTK/blob/v2.0.beta9.0/Tutorials/CNTK_106A_LSTM_Timeseries_with_Simulated_Data.ipynb). (More will come in the next Releases)
* New [CNTK NuGet Packages](./NuGet-Package.md).

See more in the [Release Notes](./ReleaseNotes//CNTK_2_0_beta_9_Release_Notes.md).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

***2017-01-19.* V 2.0 Beta 8 Release available at Docker Hub**  
CNTK V 2.0 Beta 8 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [article](./CNTK-Docker-Containers.md).

***2017-01-16.* V 2.0 Beta 8 Release**  
Highlights of this Release:
* Support of Python v. 2.7, 3.4, and 3.5. See [binary and source setup](./Setup-CNTK-on-your-machine.md) instructions to find out about how to select Python version.
* New Python API features.
* New Python example [Feature extraction using a trained model in Python API](https://github.com/Microsoft/CNTK/tree/v2.0.beta8.0/Examples/Image/FeatureExtraction).
* Support of [Visual Studio 2015](./Setup-Migrate-VS13-to-VS15.md) for Windows version.
* Introduction of [C# API in CNTK Evaluation Library](./CNTK-Library-Evaluation-on-Windows.md) and a new set of [CNTK NuGet Packages](./NuGet-Package.md).
* CNTK Runtime packages are now available as [Public Images at Docker Hub](./CNTK-Docker-Containers.md). (**Beta 7** is currently available; Beta 8 Images availability will be announced separately in a few days)
* Version 3 of [CNTK Custom MKL Library](https://www.microsoft.com/en-us/cognitive-toolkit/download-math-kernel-library/) is available.

See more in the [Release Notes](./ReleaseNotes/CNTK_2_0_beta_8_Release_Notes.md).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

***2017-01-10.* CNTK for Windows supports Visual 2015**

If you pull or merge the master branch, CNTK will now require Visual Studio 2015 to build on Windows. There are two ways to move your development environment to Visual Studio 2015:

* [Migrate VS2013 to VS2015](./Setup-Migrate-VS13-to-VS15.md): This gives you a fine grained control over where components are installed 
* [Script driven setup](./Setup-CNTK-with-script-on-Windows.md): This gives you an mostly automated migration to Visual Studio 2015

## Earlier News

* [News of 2016](./News-2016.md)
