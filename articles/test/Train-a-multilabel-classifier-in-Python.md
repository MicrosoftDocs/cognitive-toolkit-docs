For multilabel classification you should avoid using CrossEntropy as it can only handle input vectors that sum to 1. A sensible alternative is to use hamming loss.

```python
def my_hamming_loss:
    y = placeholder_variable()
    p = placeholder_variable()
    hammingLoss = reduce_sum (not_equal (y, (greater (p, 0.5))))
    return hammingLoss 
```
which you then can call as hammingLoss(labels, prob) and pass as the error metric to the Trainer.