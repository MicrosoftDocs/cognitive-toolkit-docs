---
title:   MultiGPU Support with Keras
author:    duli2012
ms.author:  duli1
ms.date:   09/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# CNTK Multi-GPU Support with Keras

Since CNTK 2.0, Keras can use CNTK as its back end, more details can be found [here](./using-cntk-with-keras.md). 
As stated in [this article](./Multiple-GPUs-and-machines), CNTK supports parallel training on multi-GPU and multi-machine. This article elaborates how to conduct
parallel training with Keras.

**Step 1.** Create a Keras model

As you see in the following code snippet, this step just uses Keras to build a model. 
```python
model = Sequential()
model.add(Dense(512, activation='relu', input_shape=(784,)))
model.add(Dropout(0.2))
model.add(Dense(512, activation='relu'))
model.add(Dropout(0.2))
model.add(Dense(10, activation='softmax'))

model.summary()

model.compile(loss='categorical_crossentropy',
              optimizer=RMSprop(),
              metrics=['accuracy'])
```

**Step 2.** Construct a distributed trainer

This step is a bit tricky, users need to explicitly construct a CNTK distributed trainer and provide it to Keras model generated in last step. To do that, we obtain the
universal learner from cntk_keras backend, wrapper it with distributed learners and feed it back to the trainer.
```python
#create a CNTK distributed trainer
model.model._make_train_function()
trainer = model.model.train_function.trainer
assert (trainer is not None), "Cannot find a trainer in Keras Model!"
learner_no = len(trainer.parameter_learners)
assert (learner_no > 0), "No learner in the trainer."
if(learner_no > 1):
    warnings.warn("Unexpected multiple learners in a trainer.")
learner = trainer.parameter_learners[0]
dist_learner = C.train.distributed.data_parallel_distributed_learner(learner
                                                     num_quantization_bits=32,
                                                         distributed_after=0)
model.model.train_function.trainer = C.trainer.Trainer(
    trainer.model, [trainer.loss_function, trainer.evaluation_function], [dist_learner])
```

**Step 3.** Partitioning and training 

Since Keras does not provide data partitioning APIs, users must do it according to their requirements and design choices. Note cntk.Communicator provides information 
about all available GPUs(workers) and the GPU the process is currently running on(rank). Users make decision on their partition strategy based on worker and rank info 
from CNTK. Below is the function that equally divides the whole data set for each GPU. 

```python
rank = cntk.Communicator.rank()
workers = cntk.Communicator.num_workers()
if (workers == 1):
    warnings.warn("Only one worker is found.")
total_items = x_train.shape[0]
start = rank*total_items//workers
end = min((rank+1)*total_items//workers, total_items)
```

Training uses standard Keras APIs just like single GPU training. At end of training, call communicator.finalize() method to conclude.
```python
history = model.fit(x_train[start : end], y_train[start : end],
                    batch_size=batch_size,
                    epochs=epochs,
                    verbose=1,
                    validation_data=(x_test, y_test))
score = model.evaluate(x_test, y_test, verbose=0)
print('Test loss:', score[0])
print('Test accuracy:', score[1])
cntk.Communicator.finalize()
```
