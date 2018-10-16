---
title:   Binary Operations
author:    n17s
ms.author:   nikosk
ms.date:   07/10/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---


# Using CNTK with Keras (Beta)

We are happy to bring CNTK as a back end for Keras as a beta release to our fans asking for this feature. While there is still feature and performance work remaining to be done, we appreciate early feedback that would help us bake Keras support. Here are the instructions for you to follow.

## Enable CNTK as Keras back end

**Step 1.** Install CNTK **>=2.0** if you haven't done so already. Earlier CNTK versions **will not work**

We assume you have a Python installation based on Anaconda. 

Choose the appropriate wheel file from the following pages to match your Python and machine environment.

- For Windows: Choose a wheel URL from [here](./Setup-Windows-Python.md)

- [For Linux]: Install the [prerequisites](./Setup-Linux-Python.md#prerequisites) and then choose a wheel URL from [here](./Setup-Linux-Python.md).

Install the wheel file

```
pip install <URL to CNTK wheel>
```

**Step 2.** Install/Update Keras in the **same environment** as CNTK.

First, if you have cntk in an environment called *cntkpy* do

For Windows:

```
activate cntkpy
```

For Linux:

```
source activate cntkpy
```

If you have a Keras installation (in the same environment as your CNTK installation), you will need to upgrade it to the latest version.

```pip install -U keras```

If you don't have Keras installed, the following command will install the latest version

```pip install keras```

**Step 3.** Update Keras to use CNTK as back end

You have two ways to set up CNTK as a Keras back end:

> 3.1. By keras.json file.

Please modify the "keras.json" file under %USERPROFILE%/.keras on Windows, or $HOME/.keras on Linux. **Only set the "backend" field to "cntk"**. If you do not have a ```keras.json```, that means you have not run Keras on this machine. Use Step 5.2 or create a .keras directory and a ```keras.json``` file with the following content.

```
{
    "epsilon": 1e-07, 
    "image_data_format": "channels_last", 
    "backend": "cntk", 
    "floatx": "float32" 
}
```

> 3.2. By environment variable

For Windows:

```
set KERAS_BACKEND=cntk
```

For Linux:

```
export KERAS_BACKEND=cntk
```

**Step 4.** Try out the Keras examples

You can try some example scripts in the Keras' repository: https://github.com/fchollet/keras/tree/master/examples

For example, clone the "mnist_mlp.py" from the link above, and run:

```python mnist_mlp.py```

## Known issues

* Performance optimization on CPU device in combination with Keras is an ongoing work item.

* Currently not supported: Gradient as symbolic ops, stateful recurrent layer, masking on recurrent layer, padding with non-specified shape (to use the CNTK backend in Keras with padding, please specify a well-defined input shape), convolution with dilation, randomness op across batch axis, few backend APIs such as `reverse`, `top_k`, `ctc`, `map`, `foldl`, `foldr`, etc.
