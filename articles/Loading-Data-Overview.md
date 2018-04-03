---
title:   Loading Data
ms.author:   minxia
ms.date:  11/30/2017
ms.topic:   conceptual
ms.devlang:   python, brainscript
---

# Loading Data

This section provides information on the processes for building input data pipelines for training in CNTK.

## Python Manuals
We include the following Python manuals in this section:
* ['Read and Feed Data to CNTK Trainer'](https://cntk.ai/pythondocs/Manual_How_to_feed_data.html) describes the different options for feeding data into the CNTK model trainers.
* ['Write a Custom Deserializer'](https://cntk.ai/pythondocs/Manual_How_to_write_a_custom_deserializer.html) goes over the steps for writing custom deserializers to load data that is in some custom format not supported by CNTK's built-in deserializers. 
* ['Create User Minibatch Sources'](https://cntk.ai/pythondocs/Manual_How_to_create_user_minibatch_sources.html) explains how to create a user minibatch source for cases where the data does not fit into memory and the user wants fine-grained control over minibatch creation.

## Additional Resources
* For more detailed examples on reading data into CNTK in **Python**, refer to the Python [tutorials](https://cntk.ai/pythondocs/tutorials.html). Some tutorials to get started include:
    * [CNTK 103D](https://cntk.ai/pythondocs/CNTK_103D_MNIST_ConvolutionalNeuralNetwork.html): Reading data in the CNTK Text Format (CTF).
    * [CNTK 201B](https://cntk.ai/pythondocs/CNTK_201B_CIFAR-10_ImageHandsOn.html): Reading image data using the CNTK image deserializer.
    * [CNTK 208](https://cntk.ai/pythondocs/CNTK_208_Speech_Connectionist_Temporal_Classification.html): Reading audio data in HTK/MLF format using the HTKFeatureDeserializer/HTKMLFDeserializer.
    * [CNTK 200](https://cntk.ai/pythondocs/CNTK_200_GuidedTour.html): An overview of the different ways to feed data to the CNTK trainer.
* For some additional information on CNTK Readers, refer to these articles on the [CTF Reader](/cognitive-toolkit/BrainScript-CNTKTextFormat-Reader) and [CNTK Binary Reader](/cognitive-toolkit/BrainScript-CNTKBinary-Reader).
