---
title:   Reasons to Switch from TensorFlow to CNTK
author:    cha-zhang 
date:    06/02/2017
ms.author:   cha-zhang
ms.date:   05/30/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   Python
---
# Reasons to Switch from TensorFlow to CNTK

Deep learning has revolutionized artificial intelligence (AI) over the past few years. Following Microsoft’s vision that AI shall be accessible for all instead of a few elite companies, we created the Microsoft Cognitive Toolkit (CNTK), an open source deep learning toolkit free for anyone to use. Today, it is the third most popular deep learning toolkit in terms of GitHub stars, behind TensorFlow and Caffe, and ahead of MxNet, Theano, Torch, etc.

Given TensorFlow’s extreme popularity, we often encounter people asking us: why would anyone want to use CNTK instead of TensorFlow? Humans have a natural tendency to follow the crowd, and there is certainly nothing wrong with it. However, in this article, we would like to point out some strong reasons in favor of CNTK, and argue that for many applications, CNTK might be a much better choice. These reasons include:

- **Speed**. CNTK is in general much faster than TensorFlow, and it can be 5-10x faster on recurrent networks.
- **Accuracy**. CNTK can be used to train deep learning models with state-of-the-art accuracy.
- **API design**. CNTK has a very powerful C++ API, and it also has both low-level and easy to use high-level Python APIs that are designed with a functional programming paradigm.
- **Scalability**. CNTK can be easily scaled over thousands of GPUs.
- **Inference**. CNTK has C#/.NET/Java inference support that makes it easy to integrate CNTK evaluation into user applications.
- **Extensibility**. CNTK can be easily extended from Python for layers and learners.
- **Built-in readers**. CNTK has efficient built in data readers that also support distributed learning.

In the remainder of this article, we expand and explain these benefits in more detail.

## Reason 1: Speed
Deep learning is data intensive and computation intensive. Whether you are building a product, or writing a paper, being able to iterate quickly is the key to success. In terms of training and evaluation speed, CNTK has significant advantages over TensorFlow. A comparison made by researchers at HKBU (http://dlbench.comp.hkbu.edu.hk/, and article: https://arxiv.org/pdf/1608.07249.pdf) showed that for all networks they tested, CNTK is superior to TensorFlow in performance, on either CPU or GPU. In fact, on GPU, CNTK is faster than all other toolkits in the comparison.

For image-related tasks, CNTK is usually 2 to 3 times faster than TensorFlow. However, on recurrent neural networks (RNN), CNTK is the undisputable winner. As reported in the above-mentioned paper, when running on CPU, “CNTK obtains much better performance (up to 5-10 times) than Torch and TensorFlow”. And on GPU, “CNTK surpasses all other tools with many times”. This speed advantage is not by accident. CNTK was originally developed by the speech group at Microsoft Research, and it has been heavily optimized to handle sequences.

If your project involves sequence processing, such as speech, natural language understanding, machine translation, etc., CNTK should be your best choice. And if you are a vision researcher working on video processing, you should definitely give CNTK a try.

## Reason 2: Accuracy
Deep learning toolkits are tricky to develop because even when there is a bug in the toolkit code you can achieve reasonable accuracy with the same network architecture design. Many other toolkits create examples that appears to be identical to the original implementation of a paper, and then offer a converted model for people to download and evaluate. This is highly irresponsible in our opinion. In CNTK we pay a lot of attention in tracking down bugs and make sure the toolkit can be used to actually train the model from scratch and achieve state-of-the-art accuracy.

One example is the Inception V3 network (https://arxiv.org/abs/1512.00567) originally developed by a few researchers at Google. TensorFlow shared the training script for Inception V3, and offered pre-trained models to download. However, it is difficult to retrain the model and achieve the same accuracy, because that requires additional understanding of details such as data pre-processing and augmentation. The best accuracy that were achieved by a third party (Keras in this case) is about 0.6% worse that what the original paper reported. Researchers in the CNTK team worked hard and were able to train a CNTK Inception V3 model with 5.972% top 5 error, even better than the original paper reported! The training script is shared as an example (https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/GoogLeNet) and you may verify it yourself.

For recurrent neural networks, CNTK’s automatic batching algorithm allows one to pack sequences with different lengths and achieve high execution efficiency. More importantly, it allows better randomization of the training data, and can often lead to 1-2% accuracy improvement compared with naïve data packing. This helped the speech group at Microsoft Research to achieve human parity in conversational speech recognition (https://arxiv.org/pdf/1610.05256.pdf).

## Reason 3: API design
TensorFlow was originally designed to have a small C++ core API, and most of its functionality is implemented in Python. This certainly has advantages, since Python is much easier to write and faster to update. The downside, however, is multiple. We already mentioned its lower speed. In addition, for many real-world applications, it is not always possible to embed Python code if the application itself is written in C++ and has tough restrictions in running time. In TensorFlow 1.0, an extensive C++ API has been released, although it remains to be somewhat slow in performance.

We envision from the very beginning that not only the evaluation of deep learning models would be an essential part of applications. The model training part can also be closely integrated into intelligent applications such as Office or Windows. CNTK is designed to have almost all functionalities written in C++. This not only makes it extremely fast, but also allows it to be used as a C++ API ready to be integrated with any applications. It also makes it very easy to add additional bindings to CNTK, such as Python, R, Java, etc.

It is also important to mention that CNTK’s Python API contains both low-level and high-level implementations. The high-level Python API is designed with a functional programming paradigm, and is highly compact and intuitive, especially when dealing with recurrent neural networks. This is in contrast with TensorFlow’s Python API, which is considered too low level by most people. TensorFlow users rely on third party high level APIs such as TensorFlow Slim and Sonnet to fill the gap, but the space is now fragmented due to too many choices.

## Reason 4: Scalability
Modern deep learning tasks could easily face billions of training examples. Being able to scale across multiple GPUs and multiple machines is a necessity. Toolkits such as TensorFlow can handle multiple GPUs on a single machine. However, when there is need to scale across multiple machines, it often becomes too complicated to fulfill.

In contrast, distributed training has always been a strong selling point for CNTK. Changing a training script from single GPU to multiple GPUs across multiple machines is merely a few lines of change, as seen in many examples in the CNTK repository. Inside Microsoft, CNTK has been used to train tasks across hundreds of GPUs on many machines. We have invented some of the most innovative schemes for parallel training, such as 1-bit SGD (http://research.microsoft.com/apps/pubs/?id=230137) and Block-Momentum SGD (https://www.microsoft.com/en-us/research/publication/scalable-training-deep-learning-machines-incremental-block-training-intra-block-parallel-optimization-blockwise-model-update-filtering/). These algorithms helped improve hyper-parameter tuning significantly, leading to better models in shorter time, such as the conversational speech recognition breakthrough by Microsoft Research (https://arxiv.org/pdf/1610.05256.pdf).

## Reason 5: Inference
TensorFlow has a very nice story about serving. It supports multiple versions of models, saving model optimized for serving, multiple meta graphs inside the same model to support serving on different devices, and plug-ins to support user customization. With their XLA AoT compilation, TF can transform the model into executable, which significantly reduces model sizes for mobile and embedded devices and latency.

Compared to TensorFlow, CNTK is more focusing on direct integration of CNTK Eval into user applications. Besides Python and C++, CNTK provides C#/.NET API for inference. The C#/.NET API is built directly upon the C++ API with least performance overhead. Java API will be available soon. If you are building a .NET application and would like to choose a deep learning toolkit for inference, CNTK is a much more natural choice than TensorFlow.

CNTK also supports parallel evaluation of multiple requests with very limited memory overhead. This provides great advantage for deploying models in a service environment like Web application. For edge devices, CNTK supports both Intel and ARM platform.

## Reason 6: Extensibility
TensorFlow is a very flexible toolkit, and one can almost implement any model with it. However, if you are a current Caffe user, you are out of luck. There is no easy way to convert your Caffe script to TensorFlow, other than rewriting everything from scratch. Similarly, if you would like to try a new layer invented by someone else and written in a different toolkit, your options are either to implement it yourself, or to wait for someone else to implement it for you.

CNTK is arguably the most extensible toolkit available as there exists no such restriction. Through UserFunctions, any operator can be implemented in pure Python. Having NumPy arrays as the interface between core CNTK and the Python bindings, you simply implement the forward and backward pass, and the newly created operator can be immediately placed into the graph. Furthermore, it is straightforward to even place other toolkit’s graph execution into a CNTK UserFunction and thus speed up the experimentation phase tremendously.

Same applies to the gradient update algorithms. Although CNTK provides most algorithms like RMSProp or Adam out of the box, you have full freedom to implement new learning approaches in pure Python.

## Reason 7: Built-in readers
Deep learning shines when there's a lot of training data. For some applications, the data is so large that it does not fit in RAM and sometimes not even on a single machine. Even when the data fits in RAM, a naïve training loop will spend a lot of its time transferring data from RAM to GPU. CNTK's built-in readers solve all the above problems by providing highly performant facilities for iterating through a dataset, without the need for the data to fit in RAM. They can be used either with a single disk or a distributed file system such as HDFS. Prefetching is used extensively so that the GPU is always utilized without stalling. CNTK's readers can also ensure that the model always receives the data in a properly shuffled order (that improves convergence) even if the underlying dataset is not shuffled. Finally, all these facilities are available to all current and future readers. Even if you write a reader for your exotic file format, you won't have to worry about implementing prefetching yourself.

To conclude the article, we promise you that if you adopt CNTK, you will be using exactly the same toolkit used by Microsoft product groups, such as Bing, Cortana, Windows, etc. We hope this will give you confidence that you are not compromised in any way because you choose CNTK. We welcome your contribution to CNTK, and together we make it the deep learning toolkit that truly democratize AI.
