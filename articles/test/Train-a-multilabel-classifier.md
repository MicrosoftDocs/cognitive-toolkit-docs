For multilabel classification you should avoid using CrossEntropy as it can only handle input vectors that sum to 1. A sensible alternative is to use a sum of logistic loss functions, one for each output
```
...
probabilities = DenseLayer {outputSize, activation=Sigmoid} (hidden)
logisticLoss = Logistic (multiLabels, probabilities)
trainingCriterion = (logisticLoss)
...
```
Apart from the loss itself you might want to monitor other metrics such as the number of incorrect predictions. There is no builtin expression for this but it can be expressed as
```
...
hammingLoss (y, p) = ReduceSum (y != (p > 0.5))
hl = hammingLoss(multiLabels,probabilities)
evaluationNodes = (hl)
...
```
This counts the number of times y[i] disagrees with p[i]>0.5. 