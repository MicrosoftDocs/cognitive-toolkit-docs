Do not use ClassificationError as it only works for multiclass labels. Using the standard arithmetic and logical operators you can express this as
```
err = (label != (prediction > 0.5))
...
evaluationNodes = (err)  
```
where we assume here that prediction is an estimate of the probability of the positive class given the input.
