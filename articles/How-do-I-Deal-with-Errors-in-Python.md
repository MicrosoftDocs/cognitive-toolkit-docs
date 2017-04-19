* [Debug a Python notebook](./How-do-I-Deal-with-Errors-in-Python#debug-a-python-notebook)?
* [Get things to work correctly when I take the last element of a sequence](./How-do-I-Deal-with-Errors-in-Python#get-things-to-work-correctly-when-i-take-the-last-element-of-a-sequence)?

## Debug a Python notebook

You will need to install ipdb via
```
pip install ipdb
```
Make sure the above installation happens for the correct python environment (the one used to run the notebook). 
Afterwards, in your notebook you should add this line to import the debugger.
```python
from IPython.core.debugger import Tracer
```
Finally, at the point where you want to debug add this line
```python
Tracer()()
```
When you run the cell with the `Tracer()()` the execution will drop into ipdb at the next line. Then you can start inspecting things with the usual [pdb](https://docs.python.org/2/library/pdb.html) commands. Don't forget to quit the debugger when you are done!

## Get things to work correctly when I take the last element of a sequence

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