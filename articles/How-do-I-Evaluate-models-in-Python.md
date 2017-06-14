---
title:   How do I evaluate models in Python
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/05/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   python
---

# How do I evaluate models in Python

* [Evaluate a saved convolutional network](#evaluate-a-saved-convolutional-network)?
* [Extract features from a specific layer using a trained model](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/FeatureExtraction)?

## Evaluate a saved convolutional network

There are a few "gotchas" with models trained on images. At this point the transformations are not part of the model so subtracting the mean has to be done manually. Another issue is that PIL loads images in a different order than what was used during training and a transposition is required. 

Assuming that: 
* during training you subtracted 128 from all channels
* the image you want to predict on is "foo.jpg"
* you saved your model with 
```python
z.save_model("mycnn.dnn")
```
then you can do the following:
```python
from cntk.ops.functions import load_model
from PIL import Image 
import numpy as np

z = load_model("mycnn.dnn")
rgb_image = np.asarray(Image.open("foo.jpg"), dtype=np.float32) - 128
bgr_image = rgb_image[..., [2, 1, 0]]
pic = np.ascontiguousarray(np.rollaxis(bgr_image, 2))

predictions = np.squeeze(z.eval({z.arguments[0]:[pic]}))
top_class = np.argmax(predictions)
```

If you are loading an old model trained by NDL or BrainScript, then you will need to find the model output node as follow:
```python
>>> for index in range(len(z.outputs)):
...     print("Index {} for output: {}.".format(index, z.outputs[index].name))
...
Index 0 for output: CE_output.
Index 1 for output: Err_output.
Index 2 for output: OutputNodes.z_output.
```
We care only about 'z_output' which has index 2. So in order to get the real model output, do the following:
```python
import cntk as ct

z_out = ct.combine([z.outputs[2].owner])
predictions = np.squeeze(z_out.eval({z_out.arguments[0]:[pic]}))
top_class = np.argmax(predictions)
```
The reason for the above, is that in old model we save the training information in addition to the actual model parameters.

## Extract features from a specific layer using a trained model?

There is an example [here](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/FeatureExtraction).
