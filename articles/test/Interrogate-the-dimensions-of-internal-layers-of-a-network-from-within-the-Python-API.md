It depends on how you use the API. If you use the layers API then a model like this:

```python
model = Sequential([
            Embedding(emb_dim),
            Recurrence(LSTM(hidden_dim),
            Dense(num_labels)
        ])
```

Can be interrogated like this:

```python
print(len(model.layers))
print(model.layers[0].E.shape)
print(model.layers[2].b.value)
```

i.e. you need to know the names of the tensors (E for embedding, b for bias, W for weights). You could recover these with some reflection though.

