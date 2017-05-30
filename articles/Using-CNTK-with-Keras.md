---
title:   Binary Operations
author:    chrisbasoglu
date:    05/24/2017
ms.author:   cbasoglu
ms.date:   05/24/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Using CNTK with Keras (Beta)

**This page is under development**

## Steps to follow

We assume you have followed the Anaconda installed on your Windows or Linux machines.
 
1. We highly recommend creating a new anaconda environment.

```conda create --name cntkkeraspy35 python=3.5 numpy scipy h5py jupyter```
 
2. Activate the new environment

```activate cntkkeraspy35```

3. Install [Keras](https://keras.io/#installation) 
 
4. Install a GPU build of CNTK

```
pip install <URL to CNTK GPU wheel>
```
 
5. Update Keras to use CNTK as backend

On Windows: 

```SET KERAS_BACKEND=cntk```

On Linux:

```export KERAS_BACKEND=CNTK```


**CHENTA** ensure “image_dim_ordering” is set to “tf”

**[DO YOU NEED THIS] If you don’t have a keras.json, it means you have not run keras on the machine. ** Run Python and import keras and it will create the file.

6.	Unzip the Keras examples to your machine

**CHENTA** Need instruction for downloading Keras examples publicly

7.	Try out the Keras examples

python c:\local\keras_examples\examples\addition_rnn.py
 
9.	Test your own scripts!
 
 
 
## Known issues:

•	The following Keras APIs are only supported on GPU: normalize_batch_in_training, batch_normalization

•	The following Keras APIs are not yet supported: clear_session, cast, resize_images, resize_volumes, print_tensor, softplus, hard_sigmod, ctc_batch_cost, ctc_decode, map_fn, foldl, foldr

•	The following examples do not work yet: mnist_swwae.py, neural_doodle.py, neural_style_transfer.py, pretrained_word_embeddings.py

•	The following Keras layers are not supported yet: convolution with dilation rate, cropping, noise, merge dot

•	CPU performance needs to be improved

•	Models cannot be saved to HDFS

