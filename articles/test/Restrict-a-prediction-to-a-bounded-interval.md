You can use `clip`. For example if you use the layers API
```python
z = Sequential([ Dense(500, activation=relu), 
                 Dense(4, activation=None), 
                 clip(Placeholder(), 0, 224) ])
```
will create a network with one layer with relu's and one layer with linear activations. The latter has four outputs whose predictions are limited in the interval [0,224]. This could be used to predict bounding boxes for images of size 224 x 224.