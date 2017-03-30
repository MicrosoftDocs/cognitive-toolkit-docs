If I create a model with some input_variable and then later from the trainer, I need the input_variable name, but I only have the model around, how can I introspect to get at the input_variables?

Say for example, you setup your trainer like this:
```python
SetupTrainer():
    input = cntk.input_variable((input_dim), np.float32)
    label = cntk.input_variable((num_output_classes), np.float32)

    z = model(input)   # (features) -> (prediction as unnormalized log prob)
    ce = cross_entropy_with_softmax(z, label)
    errs = classification_error(z, label)
    criterion = combine ([ce, errs]) # (features, labels) -> (loss, metric)

    trainer = Trainer(model, criterion.outputs[0], criterion.outputs[1], learner)

    return trainer, criterion
```

Then later, you needed to introspect to get back the names input and label by using “[arguments](https://www.cntk.ai/pythondocs/graph.html#cntk.ops.functions.Function)”:
```python
# train the model
trainer, criterion = SetupTrainer()
trainer.train_minibatch({criterion.arguments[0]: features, criterion.arguments[1]: labels})  
```