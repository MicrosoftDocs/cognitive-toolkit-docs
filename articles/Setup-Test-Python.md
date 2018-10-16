---
title:   Setup Test Python
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/03/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   quickstart
ms.devlang:   python
---

# Setup Test Python

## Testing your CNTK install with Python

We assume you have Python and CNTK installed on your machine, as well as the CNTK samples and tutorials. The Python installation is included in your PATH statement; if you have installed CNTK in a Python environment you will need to activate that Python environment before you run the samples.

- Open a standard command prompt
- Activate the Python environment if necessary
- Change into the sample directory

### Run an example

Change into the `Tutorials/NumpyInterop` directory and run the FeedForward example:
```
cd Tutorials
cd NumpyInterop
python FeedForwardNet.py
```
You will see the following output on the console:
```
Minibatch[   1- 128]: loss = 0.564038 * 3200
Minibatch[ 129- 256]: loss = 0.308571 * 3200
Minibatch[ 257- 384]: loss = 0.295577 * 3200
Minibatch[ 385- 512]: loss = 0.270765 * 3200
Minibatch[ 513- 640]: loss = 0.252143 * 3200
Minibatch[ 641- 768]: loss = 0.234520 * 3200
Minibatch[ 769- 896]: loss = 0.231275 * 3200
Minibatch[ 897-1024]: loss = 0.215522 * 3200
Finished Epoch [1]: loss = 0.296552 * 25600
error rate on an unseen minibatch 0.040000
```

### Run Jupyter notebooks

CNTK contains several tutorials based on Jupyter notebooks. To use them, execute the following commands:
```
cd Tutorials
jupyter notebook
```
This will start a browser with the available notebooks. 

