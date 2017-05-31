---
title:   Binary Operations
author:    sayanpathak
date:    05/30/2017
ms.author:   sayanpa
ms.date:   05/30/2017
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

```pip install git+https://github.com/souptc/keras.git```

This is a public github repo which host cntk keras support. We are working with keras team to contribute this back soon.

4. Install a GPU build of CNTK

```pip install <URL to CNTK GPU wheel>```

5. Update Keras to use CNTK as backend

You have two ways to set keras backend:

> 5.1. By keras.json file.

>   Please modify the "keras.json" file under %Your User Folder%/.keras, (If you don't have a keras.json, that means you have not run keras on this machine). **Only set the "backend" field to "cntk"**

```
   { 
    "epsilon": 1e-07, 
    "image_data_format": "channels_last", 
    "backend": "cntk", 
    "floatx": "float32" 
    }
```
	

> 5.2. By environment variable

>> On Windows:

> ```SET KERAS_BACKEND=cntk```

>> On Linux:

> ```export KERAS_BACKEND=cntk```


7.	Try out the Keras examples

You can try some example scripts in keras' repo: 
https://github.com/fchollet/keras/tree/master/examples 
 
For example, clone the "imdb_lstm.py" from the link above, and run: 
```
python imdb_lstm.py 
```

## Known issues:

•	Performance optimization on CPU device 

•	The following operations are currently not supported in the the beta release.

> Gradient as symbolic ops, Masking on recurrent layer, Padding with non-specified shape (to use cntk backend in keras with padding, please specify a well-defined input shape), Convolution with dilation, Randomness op across batch axis, few backend apis such as reverse, top_k, ctc, map, foldl, foldr etc.
