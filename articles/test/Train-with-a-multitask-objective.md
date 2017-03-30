You can just define your combined criterion as a BrainScript expression and you can monitor all individual task losses by specifying them as `evaluationNodes`.
```
task1loss = CrossEntropyWithSoftMax(prediction,label)
task2loss = SquareError(reconstruction,input)
mtloss = task1loss + Constant(0.3) .* task2loss 
criterionNodes = (mtloss)
evaluationNodes = (task1loss:task2loss)
```
