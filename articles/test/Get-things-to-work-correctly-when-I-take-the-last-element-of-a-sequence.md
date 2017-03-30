There are two issues here. Support that you have a sequence `seq`. You want to take the last element, further process it with a few layers.
```python
last = C.sequences.last(seq)
z = a_few_layers(last)
```
Now you want to plug `z` in your loss but you get an error about dynamic axes. Input variables are created with some default dynamic axes but `last` (and `z`) had its dynamic axes determined by `sequences.last`. So one possibility is to define the label variable at this point and have it copy its dynamic axes from `z`. Typically:
```python
y = C.input_variable(z.shape, dynamic_axes=z.dynamic_axes)
loss = C.squared_error(y, z)
```
Finally, when training, the data labels must have a dynamic axis of size one i.e. each element in the batch should have a shape of (1,)+y.shape. 