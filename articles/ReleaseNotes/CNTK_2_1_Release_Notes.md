---
title:   CNTK_2_1_Release_Notes
author:    alexeyo26
ms.author:   alexeyo
ms.date:   07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

## CNTK v.2.1 Release Notes

This is a summary of changes and new features in the Microsoft Cognitive Toolkit V.2.1.

### Highlights of this Release

* cuDNN 6.0 integration
* Support of Universal Windows Platform (UWP)
* Improvements in the CNTK Keras backend
* Performance improvements
* New manuals, tutorials and examples
* Bug fixes

## Breaking changes

This release contains the following **breaking changes**:

* Updated ROI pooling to match Caffe implementation. The signature has the following updates:
  * the parameters `pooling_type` and `spatial_scale` were added, and
  * the coordinates for the parameters `rois` are now absolute to the original image size
* C++ API. `NDShape::Unknown` was changed to `NDShape::Unknown()` to remove a static variable from the header.

## New and updated features

* Reinforcement learning framework for CNTK. See details [here](https://github.com/Microsoft/CNTK/blob/release/2.1/bindings/python/cntk/contrib/deeprl/README.md)
* More flexible Python-based user deserializer. See the [manual](https://github.com/Microsoft/CNTK/blob/release/2.1/Manual/Manual_How_to_write_a_custom_deserializer.ipynb) for detailed information
* Caffe to CNTK model converter. See the [Converter ReadMe](https://github.com/Microsoft/CNTK/blob/release/2.1/bindings/python/cntk/contrib/crosstalkcaffe/README.md) Examples, illustrating the usage of the converter are available in [CNTK Codebase](https://github.com/Microsoft/CNTK/tree/release/2.1/bindings/python/cntk/contrib/crosstalkcaffe/examples). 
* Reduction over multiple axes:
  * Computes the mean of the input tensor's elements across the specified axis
  * Computes the mean of the input tensor's elements across a specified axis or a list of specified axes 
* Support of Universal Windows Platform (UWP) in CNTK Evaluation library (see section below)
* New and improved features in the CNTK Keras backend (see section below)

### NVIDIA cuDNN 6.0 integration

GPU editions of CNTK Version 2.1 on Windows and Linux are shipped with the [NVIDIA CUDA Deep Neural Network library (cuDNN) v.6.0](https://developer.nvidia.com/cudnn). This improves CNTK performance with networks like ResNet 50 by about 10%.

If you build CNTK from source, you should also install NVIDIA cuDNN 6.0, since it is now the [default for CNTK build and test](https://docs.microsoft.com/en-us/cognitive-toolkit/Setup-CNTK-on-Windows#cudnn).

### CNTK Evaluation library. Support of Universal Windows Platform (UWP)

With this release we introduce the support of [Universal Windows Platform (UWP)](https://docs.microsoft.com/en-us/windows/uwp/get-started/whats-a-uwp). A new CNTK NuGet Package *[CNTK, UWP CPU-Only Build](http://www.nuget.org/packages/CNTK.UWP.CPUOnly)* is available for download. For further details see the description of [Model Evaluation on Universal Windows Platform](https://docs.microsoft.com/en-us/cognitive-toolkit/CNTK-Library-Evaluation-on-UWP). To build the CNTK UWP evaluation library, see the description [here](https://docs.microsoft.com/en-us/cognitive-toolkit/Setup-UWP-Build-on-Windows).

### CNTK backend for Keras

Version 2.1 introduces the following improvements for the [CNTK backend for Keras](https://docs.microsoft.com/en-us/cognitive-toolkit/Using-CNTK-with-Keras):

* Stateful recurrent network support
* Support of Recurrent layer with mask
* Fixing batch normalization layer issue

### Performance improvements

* ResNet 50 performance improvement (reduce memcpy and memset during training). Expect single machine training speed to improve by about 8%
* Improvements in CNTK reader by index caching
* We have successfully tested CNTK ability to train ResNet 50 and Inception V3 with large minibatch size as the proposed in a [recent paper]( https://research.fb.com/publications/imagenet1kin1h/). This confirms CNTK ability of minibatch scaling not only for speech, but for image tasks as well

### New manuals, tutorials, examples and courses

#### Manuals

* [How to train model using declarative and imperative API](https://github.com/Microsoft/CNTK/blob/release/2.1/Manual/Manual_How_to_train_using_declarative_and_imperative_API.ipynb)
* [How to create user minibatch sources](https://github.com/Microsoft/CNTK/blob/release/2.1/Manual/Manual_How_to_create_user_minibatch_sources.ipynb)
* [How to feed data](https://github.com/Microsoft/CNTK/blob/release/2.1/Manual/Manual_How_to_feed_data.ipynb)
* [Debugging CNTK programs](https://github.com/Microsoft/CNTK/blob/release/2.1/Manual/Manual_How_to_debug.ipynb)
* [How to use learners](https://github.com/Microsoft/CNTK/blob/release/2.1/Manual/Manual_How_to_use_learners.ipynb)
* [How to write a custom deserializer](https://github.com/Microsoft/CNTK/blob/release/2.1/Manual/Manual_How_to_write_a_custom_deserializer.ipynb)

#### Tutorials and Examples

* [Training Acoustic Model with Connectionist Temporal Classification (CTC) Criteria](https://github.com/Microsoft/CNTK/blob/release/2.1/Tutorials/CNTK_208_Speech_Connectionist_Temporal_Classification.ipynb)
* [Flapping Bird using Keras and Reinforcement Learning](https://github.com/Microsoft/CNTK/tree/release/2.1/Examples/ReinforcementLearning/FlappingBirdWithKeras)
* [Faster R-CNN object detection](https://github.com/Microsoft/CNTK/tree/release/2.1/Examples/Image/Detection/FasterRCNN)
* [Using CNTK V2 API in Azure WebAPI for model evaluation](https://docs.microsoft.com/en-us/cognitive-toolkit/Evaluate-a-model-in-an-Azure-WebApi)
* [CVPR 2017](http://cvpr2017.thecvf.com/program/tutorials) - July 26, 2017, Tutorial by *Emad Barsoum*, *Sayan Pathak* and *Cha Zhang*, Scalable Deep Learning with Microsoft Cognitive Toolkit. [Presentation](https://www.cntk.ai/Tutorials/CVPR2017/CVPR_2017_Tutorial_final.pdf)

#### Courses

New course [Deep Learning Explained](https://www.edx.org/course/deep-learning-explained-microsoft-dat236x) is published at [edX](https://www.edx.org/).

### Bug fixes

Version 2.1 provides fixes for the following bugs:

* Concurrency issue in parallel evaluations
* Intermittent reference error in `next_minibatch`

### CNTK NuGet package

A new set of NuGet Packages (version 2.1.0) is provided with this release, including the new package *CNTK, UWP CPU-Only Build* (see section on Support of UWP above).

### Acknowledgments

We thank the following community members for their [contributions to CNTK V.2.1](https://github.com/Microsoft/CNTK/pulls?utf8=%E2%9C%93&q=is%3Apr%20is%3Amerged%20merged%3A2017-06-01..2017-07-28):

* [@arturl](https://github.com/arturl)
* [@boeddeker](https://github.com/boeddeker)
* [@chivee](https://github.com/chivee)
* [@dadebarr](https://github.com/dadebarr)
* [@DGideas](https://github.com/dgideas)
* [@frankibem](https://github.com/frankibem)
* [@GeoffChurch](https://github.com/GeoffChurch)
* [@imriss](https://github.com/imriss)
* [@juice500ml](https://github.com/juice500ml)
* [@lakshayg](https://github.com/lakshayg)
* [@makrei](https://github.com/makrei)
* [@michhar](https://github.com/michhar)
* [@mikhail-barg](https://github.com/mikhail-barg)
* [@souptc](https://github.com/souptc)
* [@taehoonlee](https://github.com/taehoonlee)
* [@vermorel](https://github.com/vermorel)
* [@vmazalov](https://github.com/vmazalov)
* [@wsywl](https://github.com/wsywl)
* [@yuxiaoguo](https://github.com/yuxiaoguo)

We apologize for any contribution we might have overlooked.