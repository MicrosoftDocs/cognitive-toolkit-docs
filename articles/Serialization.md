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

This article goes over saving and loading models and checkpointing during training.

## Saving and loading models

### Save model

To save a model to file, use the [`save()`](https://cntk.ai/pythondocs/cntk.ops.functions.html#cntk.ops.functions.Function.save) function and specify a filepath for the saved model.
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
Alternatively, you can also load your model using [`load()`](https://www.cntk.ai/pythondocs/cntk.ops.functions.html?#cntk.ops.functions.Function.load):
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

In CNTK, one way to save the model state is by using the aformentioned [`save()`](#save-model) method. However, this method only saves the model state, and not other stateful entities of the training script like the current state of the minibatch source and the trainer, which are also needed in order to restore a state to resume training. To this end, CNTK provides checkpointing API for the two ways of model training: 1) the low-level `Trainer.train_minibatch` API  and 2) the high-level `Function.train` (`training_session`) API. For more information on the different training methods, see the manual ['Train model using declarative and imperative API.'](https://cntk.ai/pythondocs/Manual_How_to_train_using_declarative_and_imperative_API.html)

### Low-level checkpointing (`Trainer.train_minibatch`)
In the [Trainer.train_minibatch/test_minibatch](https://www.cntk.ai/pythondocs/cntk.train.trainer.html?#cntk.train.trainer.Trainer.train_minibatch) method of training, the user has full control over each minibatch update and data is fed to the trainer through an explicit loop, minibatch by minibatch. In this case, to perform checkpointing during training, the user has to manually checkpoint the current state of both the minibatch source and the trainer.

**Save checkpoint**

Once your Trainer object is instantiated, during training you can checkpoint the model and trainer state by calling the [`save_checkpoint`](https://cntk.ai/pythondocs/cntk.train.trainer.html#cntk.train.trainer.Trainer.save_checkpoint) method while inside your training loop:
```Python
# get the checkpoint state of the minibatch source (mb_source)
mb_source_state = mb_source.get_checkpoint_state()

# pass the minibatch source state to the external_state parameter of save_checkpoint()
checkpoint = "myModel.dnn" #filename to store the checkpoint
trainer.save_checkpoint(checkpoint, mb_source_state)
```

**Restore from checkpoint**

To resume training from a checkpoint file, restore the minibatch source and trainer state using the corresponding [`restore_from_checkpoint`](https://www.cntk.ai/pythondocs/cntk.train.trainer.html?#cntk.train.trainer.Trainer.restore_from_checkpoint) methods:

```Python
checkpoint = "myModel.dnn"
mb_source_state = trainer.restore_from_checkpoint(checkpoint)
mb_source.restore_from_checkpoint(mb_source_state)
```

For a complete example of manual checkpointing during training, refer [here](https://cntk.ai/pythondocs/Manual_How_to_train_using_declarative_and_imperative_API.html).

### High-level checkpointing (`Function.train`)
Instead of explicitly writing the training loop, the user can use the [Function.train/test](https://www.cntk.ai/pythondocs/cntk.ops.functions.html?#cntk.ops.functions.Function.train) methods, which take care of the different aspects of a training session, including data sources, checkpointing, and progress printing. 

In order to enable checkpointing, the user must provide a checkpoint configuration callback by instantiating the [`CheckpointConfig`](https://www.cntk.ai/pythondocs/cntk.train.training_session.html?highlight=checkpointconfig#cntk.train.training_session.CheckpointConfig) class. The callback then takes care of consistent checkpointing with the specified frequency during training. To restore from the last available checkpoint before the start of training, the `restore` parameter is default set to `True`. If you would like to save all the checkpoints from training, set `preserve_all` to `True` (default is `False`). The checkpoint filenames are then saved like so: e.g. if `filename="myModel.dnn"`, the checkpoints will be `myModel.dnn.0`, `myModel.dnn.1`, `myModel.dnn.2`, and so on.

Once you've defined your minibatch source (`mb_source`), model (`z`), criterion function (`criterion`), learner and input map (if `mb_source` is a data reader), you can configure the train method to take in a checkpoint callback:
```Python
checkpoint = "myModel.dnn"
checkpoint_frequency = 100
checkpoint_config = C.CheckpointConfig(checkpoint, frequency=checkpoint_frequency, preserve_all=True)
criterion.train(mb_source, model_inputs_to_streams = input_map, parameter_learners=[learner], callbacks=[checkpoint_config]) 
```

Note that [`Function.train`](https://www.cntk.ai/pythondocs/cntk.ops.functions.html?#cntk.ops.functions.Function.train) has additional parameters that can be specified.

More detailed examples can be found [here](https://cntk.ai/pythondocs/Manual_How_to_train_using_declarative_and_imperative_API.html) (in section 2) and in the [CNTK 200 Tutorial](https://cntk.ai/pythondocs/CNTK_200_GuidedTour.html) (under the "Advanced Training Example").

**Note on `training_session`**

[`Function.train`](https://www.cntk.ai/pythondocs/cntk.ops.functions.html?#cntk.ops.functions.Function.train) is a convenience wrapper around [`cntk.train.trainer.Trainer`](https://www.cntk.ai/pythondocs/cntk.train.trainer.html#cntk.train.trainer.Trainer) and [`cntk.train.training_session.TrainingSession`](https://www.cntk.ai/pythondocs/cntk.train.training_session.html#cntk.train.training_session.TrainingSession), which encapsulates a typical training loop. You may sometimes come across examples that explicitly instantiate a `TrainingSession` object using [`training_session`](https://www.cntk.ai/pythondocs/cntk.train.training_session.html#cntk.train.training_session.training_session).  In that case, the above `Function.train` training code can equivalently be expressed as such:

```Python
trainer = C.Trainer(z, criterion, [learner])
checkpoint_frequency = 100
checkpoint_config = C.CheckpointConfig(checkpoint, frequency=checkpoint_frequency, preserve_all=True)

C.train.training_session(
  trainer=trainer,
  mb_source=mb_source,
  mb_size=mb_size,
  model_inputs_to_streams=input_map,
  checkpoint_config=checkpoint_config
).train()
```
