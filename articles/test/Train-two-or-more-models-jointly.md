There are two cases that this can arise. One is multitask learning the other is you have one processing pipeline for some part of your input and another pipeline for the other. 

  * Multitask learning is when you have multiple criteria you want your network to be good at. The standard way to deal with this is to construct a single number out of all these criteria. For example
```python
cross_entropy = cross_entropy_with_softmax(predictions, labels)
reconstruction_error = squared_error(reconstruction, features)
combined_loss = 0.3 * reconstruction_error + cross_entropy
```
  * Multiple pipelines such as the one below are also tricky 
```python
q_embed = qmodel(question)
a_embed = amodel(answer)
```

The trainer takes a single function whose parameters should be modified by the training proceduce. How can we pass the union of `qmodel` and `amodel` or the union of `cross_entropy` and `reconstruction_error`? The answer is to find a point where all the parameters are part of the same function. That happens at the very least in the final loss.
i.e. `loss.parameters` contains all parameters the loss depends on. Typically however our model should not 
include the loss, as it only makes sense for training. Therefore a better approach is to use combine to create a combined model
```python 
final_model = combine(predictions, reconstruction)
```
For the separate pipeline case there is probably a place where everything gets combined
```python 
qa_score = score(q_embed,a_embed)
```
then `qa_score` can play the role of `final_model` above.