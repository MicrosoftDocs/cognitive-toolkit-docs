[Read and modify the training weights from Python](./How-do-I-Adapt-models-in-Python#read-and-modify-the-training-weights-from-python)

## Read and modify the training weights from Python

```python
from cntk import *
p=parameter(5, init=glorot_uniform())

p.value
>>>result: array([-0.7146188 ,  0.59619093,  0.95851505,  0.29351783,  0.13692594], dtype=float32)

p.value = np.ones(5)
```
