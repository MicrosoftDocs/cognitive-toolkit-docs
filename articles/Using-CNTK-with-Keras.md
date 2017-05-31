---
title:   Binary Operations
author:    sayanpa
date:    05/30/2017
ms.author:   sayanpa
ms.date:   05/30/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

**These instructions are subject to change as we are working with the Keras maintainers to integrate the CNTK backend in the official Keras repository**.

# Using CNTK with Keras (Beta)

We are happy to bring CNTK as a backend for Keras as a beta release to our fans asking for this feature. While there is still feature and performance work remaining to be done, we appreciate early feedback that would help us bake Keras support. Here are the instructions for you to follow.

## Steps to follow

We assume you have followed the Anaconda installation instructions on your Windows or Linux machines.

Steps 1 and 2 can be optional. We **strongly** urge you to follow them especially if you have an **existing Keras installation**. 

Step 1. We highly recommend starting by creating a new anaconda environment. The conda environment allows you to use the feature without interfering with your default python setup (or any existing Keras installation).  

```conda create --name cntkkeraspy35 python=3.5 numpy scipy h5py jupyter```

Step 2. Activate the new environment, if you have created an environment. If you have chosen to install Keras (CNTK) in default environment, then skip to the next step. 

```activate cntkkeraspy35```

Step 3. Install our fork of Keras (we are working on getting this integrated in the official Keras repository)

```pip install git+https://github.com/souptc/keras.git```

Step 4. Install a suitable build of CNTK (older CNTK versions don't have Keras support).

```pip install <URL to CNTK wheel>```

Please choose one of the several available wheel options from the following pages to match your python + machine environment.

> For Windows: The binaries and install instructions can be found [here](./Setup-Windows-Python.md).

> For Linux: The binaries are found [here](./Setup-Linux-Python.md). Additionally please follow the pre-requisites section from this page before installing the wheel. 

Step 5. Update Keras to use CNTK as backend

You have two ways to set keras backend:

> 5.1. By keras.json file.

Please modify the "keras.json" file under %USERPROFILE%/.keras on windows, or $HOME/.keras on Linux. **Only set the "backend" field to "cntk"**. If you do not have a ```keras.json```, that means you have not run keras on this machine. Use step 5.2 or create a .keras directory and a ```keras.json``` file with the following content.

``` 
{ 
    "epsilon": 1e-07, 
    "image_data_format": "channels_last", 
    "backend": "cntk", 
    "floatx": "float32" 
} 
```	

> 5.2. By environment variable

> On Windows:

> ```SET KERAS_BACKEND=cntk```

> On Linux:

> ```export KERAS_BACKEND=cntk```

Step 6. Try out the Keras examples

You can try some example scripts in keras' repo: 
https://github.com/fchollet/keras/tree/master/examples 
 
For example, clone the "imdb_lstm.py" from the link above, and run: 

```python imdb_lstm.py ```

## Known issues:

•	Performance optimization on CPU device 

•	The following operations are currently not supported in the the beta release.

> Gradient as symbolic ops, Masking on recurrent layer, Padding with non-specified shape (to use cntk backend in keras with padding, please specify a well-defined input shape), Convolution with dilation, Randomness op across batch axis, few backend apis such as reverse, top_k, ctc, map, foldl, foldr etc.
