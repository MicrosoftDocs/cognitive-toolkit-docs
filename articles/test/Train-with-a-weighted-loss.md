The specification of a per-example weight in the loss is as simple as
```python
weight = input_variable((1))
weighted_loss = weight * loss
```
where `loss` is any builtin or user-defined loss function. Of course during training each minibatch will need to have a mapping from weight to actual values (one for each example).