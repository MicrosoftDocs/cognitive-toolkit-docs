---
title:   MultiGPU Support with Keras
author:    duli2012
ms.author:  duli1
ms.date:   09/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
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

This step is a bit tricky, users need to explicitly construct a CNTK distributed trainer and provide it to Keras model generate in last step. Here, the trainer
uses [BlockMomentumSGD algorithm](./Multiple-GPUs-and-machines#6-block-momentum-sgd). User can easily choose other parallel SGD algorithms support by CNTK.
```python
#create a CNTK distributed trainer
model.model._make_train_function()
trainer = model.model.train_function.trainer
learner = trainer.parameter_learners[0]
dist_learner = C.train.distributed.data_parallel_distributed_learner(learner,
                                                    num_quantization_bits=32,
                                                         distributed_after=0)
model.model.train_function.trainer = C.trainer.Trainer(trainer.model, 
				[trainer.loss_function, trainer.evaluation_function], [dist_learner])

```

**Step 3.** Partitioning and training 
Since Keras does not provide data partitioning APIs, users must do it according to their requirements and design choices. Below is the function we used to partition data
in this example.
```python
rank = C.Communicator.rank()
workers = C.Communicator.num_workers()
total_items = x_train.shape[0]
start = rank*total_items//workers
end = min((rank+1)*total_items//workers, total_items)
```

Similiar to Step 2, training uses standard Keras APIs just like single GPU training.
```python
history = model.fit(x_train[start : end], y_train[start : end],
                    batch_size=batch_size,
                    epochs=epochs,
                    verbose=1,
                    validation_data=(x_test, y_test))
score = model.evaluate(x_test, y_test, verbose=0)
print('Test loss:', score[0])
print('Test accuracy:', score[1])
```