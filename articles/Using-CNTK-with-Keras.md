---
title:   Binary Operations
author:    sayanpa
ms.author:   sayanpa
ms.date:   05/30/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

**These instructions are subject to change the CNTK back end is integrated in the official Keras repository**.

# Using CNTK with Keras (Beta)

We are happy to bring CNTK as a back end for Keras as a beta release to our fans asking for this feature. While there is still feature and performance work remaining to be done, we appreciate early feedback that would help us bake Keras support. Here are the instructions for you to follow.

## Enable CNTK as Keras back end

We assume you have followed the Anaconda installation instructions on your Windows or Linux machines.

Steps 1 and 2 can be optional. We **strongly** urge you to follow them especially if you have an **existing Keras installation**.

**Step 1.** We highly recommend starting by creating a new anaconda environment. The conda environment allows you to use the feature without interfering with your default Python setup (or any existing Keras installation).

```conda create --name cntkkeraspy35 python=3.5 numpy scipy h5py jupyter```

**Step 2.** Activate the new environment, if you have created an environment. If you have chosen to install Keras (CNTK) in default environment, then skip to the next step.

For Windows:

```
activate cntkkeraspy35
```

For Linux:

```
source activate cntkkeraspy35
```

**Step 3.** Install the latest master branch of Keras

```pip install git+https://github.com/fchollet/keras.git```

**Step 4.** Install CNTK version 2.0 (GA or later) into the activated environment

Choose the appropriate wheel file from the following pages to match your Python and machine environment.

- For Windows: Choose a wheel URL from [here](./Setup-Windows-Python.md)

- [For Linux]: Install the [prerequisites](./Setup-Linux-Python.md#prerequisites) and then choose a wheel URL from [here](./Setup-Linux-Python.md).

Install the wheel file

```
pip install <URL to CNTK wheel>
```

**Step 5.** Update Keras to use CNTK as back end

You have two ways to set up CNTK as a Keras back end:

> 5.1. By keras.json file.

Please modify the "keras.json" file under %USERPROFILE%/.keras on Windows, or $HOME/.keras on Linux. **Only set the "backend" field to "cntk"**. If you do not have a ```keras.json```, that means you have not run Keras on this machine. Use Step 5.2 or create a .keras directory and a ```keras.json``` file with the following content.

```
{
    "epsilon": 1e-07, 
    "image_data_format": "channels_last", 
    "backend": "cntk", 
    "floatx": "float32" 
}
```

> 5.2. By environment variable

For Windows:

```
set KERAS_BACKEND=cntk
```

For Linux:

```
export KERAS_BACKEND=cntk
```

**Step 6.** Try out the Keras examples

You can try some example scripts in the Keras' repository: https://github.com/fchollet/keras/tree/master/examples

For example, clone the "mnist_mlp.py" from the link above, and run:

```python mnist_mlp.py```

## Known issues

* Performance optimization on CPU device in combination with Keras is an ongoing work item.

* Currently not supported: Gradient as symbolic ops, stateful recurrent layer, masking on recurrent layer, padding with non-specified shape (to use the CNTK backend in Keras with padding, please specify a well-defined input shape), convolution with dilation, randomness op across batch axis, few backend APIs such as `reverse`, `top_k`, `ctc`, `map`, `foldl`, `foldr`, etc.
