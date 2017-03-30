A temperature softmax is very easy in BrainScript
```
TemperatureSoftmax (z, T) = Softmax (z / Constant (T))
```