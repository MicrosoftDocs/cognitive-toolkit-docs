Implementing an attention mechanism requires computing a softmax over a dynamic axis. One way to do this is with a recurrence. Symbolic recurrences in Python take a little while to get used to. To make things concrete let's see how one might go about implementing a model that takes a query and a candidate answer and computes the cosine similarity of their representations. First we assume that the query and the answer have been processed by pipelines like this
```python
q_lstm = Sequential([ Embedding(500), BiRecurrence(LSTM(300), LSTM(300)), Dense(200)])
a_lstm = Sequential([ Embedding(500), BiRecurrence(LSTM(300), LSTM(300)), Dense(200)])
q_embed = q_lstm(question)
a_embed = a_lstm(answer) 
```
where `BiRecurrence` is a convenience function that you can find in the solution of the third task of [this tutorial](https://github.com/Microsoft/CNTK/blob/v2.0.beta15.0/Tutorials/CNTK_202_Language_Understanding.ipynb). It runs one LSTM forward, another LSTM backward and concatenates the results. After this preprocessing we have a variable-length sequence of 200 dimensional vectors for the query and another variable length sequence of 200 dimensional vectors for the answer.

To implement an attention mechanism we need to compute scalar values for each position and exponentiate them with an appropriate correction so that the sum of their exponentials equals 1.
```python
w_q = C.parameter((200,1), init=C.glorot_normal())
w_a = C.parameter((200,1), init=C.glorot_normal())
zq = C.times(q_embed, w_q)
za = C.times(a_embed, w_a)
```
Now we need to compute the appropriate correction which is the log of the sum of the exponentials. This can be done with another recurrence. 
```python
p = C.placeholder_variable((1))
prev_zq_or_tiny = C.element_select(C.sequence.is_first(zq), -1e+30, C.past_value(p))
log_cumsum_exp = C.log_add_exp(zq, prev_zq_or_tiny)
actual_log_cumsum_exp = log_cumsum_exp.replace_placeholders({p:log_cumsum_exp.output})
log_sum_exp = C.sequence.last(actual_log_cumsum_exp)
attn_q = C.exp(zq - C.sequence.broadcast_as(log_sum_exp , zq))
```
The hardest part to understand is the call to `replace_placeholders`. 
Before this call this part of the computation graph did not contain a loop: we were
either looking at `zq` or the past value of `p`. Once we call replace_placeholders we close the loop and make
`p` point to the output of the expression it was used to define!

The attention weights `attn_a` can be obtained in the same fashion. Finally, we can compute the cosine distance between 
the attended embeddings as:
```python
attended_q = C.sequence.reduce_sum(attn_q * q_embed)
attended_a = C.sequence.reduce_sum(attn_a * a_embed)
cosine_dst = C.cosine_distance(attended_q, attended_a)
```
 

