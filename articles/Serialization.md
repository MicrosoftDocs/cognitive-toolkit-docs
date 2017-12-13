---
title:   Serialization
author:    mx-iao
ms.author:   minxia
ms.date:   12/11/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   python
---

# Serialization

## Saving and loading models

### Save model

To save a model to file, use the [save()](https://cntk.ai/pythondocs/cntk.ops.functions.html#cntk.ops.functions.Function.save) function and specify a filepath for the saved model.
```Python
import cntk as C

x = C.input_variable(<input shape>)
z = create_model(x) #user-defined 
z.save("myModel.model")
``` 

Note that a model saved in this way using the the [CNTK Library API](https://docs.microsoft.com/en-us/cognitive-toolkit/cntk-library-api) will have the model-v2 format. The model-v2 format is a Protobuf-based model serialization format, introduced in CNTK v2. (For more information, refer to [CNTK Model Format](https://docs.microsoft.com/en-us/cognitive-toolkit/CNTK-model-format).) The file extension for the saved model filepath is irrelevant - across CNTK documentation you will see the use of `.model`, `.dnn`, `.cmf`, etc.

### Load model
To load a model from file into CNTK:
```Python
from cntk.ops.functions import load_model

z = load_model("myModel.model")
```
Alternatively, you can also load your model using [load()](https://cntk.ai/pythondocs/cntk.ops.functions.html?highlight=load_model#cntk.ops.functions.Function.load):
```Python
z = C.Function.load("myModel.model")
```
### Related resources
The following examples include some commonly performed tasks involving the saving and loading of trained models.
* [Evaluate a saved trained model](https://docs.microsoft.com/en-us/cognitive-toolkit/How-do-I-Evaluate-models-in-Python#evaluate-a-saved-convolutional-network)
* [Access the parameters of a saved trained model](https://docs.microsoft.com/en-us/cognitive-toolkit/How-do-I-Read-Things-in-Python#load-model-and-access-network-weights-parameters)
* [Evaluate and write out specific layers of a saved trained model](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/FeatureExtraction)  

### ONNX format
CNTK also supports the saving and loading of models in the [ONNX](http://onnx.ai/) format, which allows for interoperability among other frameworks, including Caffe2, PyTorch and MXNet. 

To save a model to the ONNX format, simply specify the format parameter:
```Python
z.save("myModel.onnx", format=C.ModelFormat.ONNX)
```
And to load a model from ONNX:
```Python
z = C.Function.load("myModel.onnx", format=C.ModelFormat.ONNX)
```
You can find more ONNX-specific tutorials [here](https://github.com/onnx/tutorials).


## Checkpointing during training
During the training of deep neural networks, the practice of checkpointing allows the user to take snapshots of the model state and weights across regular intervals. Since the training of deep learning models can be extremely time-consuming, checkpointing ensures a level of fault tolerance in the event of hardware or software failures. Moreover, checkpointing enables the user to resume training from the last saved checkpoint, and to retain the best-performing models (i.e. during hyperparameter tuning).

In CNTK, one way to save the model state is by using the aformentioned [`save()`](#save-model) method. However, this method only saves the model state, and not other stateful entities of the training script like the current state of the minibatch source and the trainer, which are also needed in order to restore a state to resume training. To this end, CNTK provides checkpointing API for the two ways of model training: 1) the low-level `Trainer.train_minibatch` API  and 2) the high-level `Function.train` (training_session) API. For more information on the different training methods, see the manual ['Train model using declarative and imperative API'](https://cntk.ai/pythondocs/Manual_How_to_train_using_declarative_and_imperative_API.html).

### Low-level checkpointing (`Trainer.train_minibatch`)

### High-level checkpointing (`Function.train`, `training_session`)
