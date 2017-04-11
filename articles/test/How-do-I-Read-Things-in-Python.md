[Load model and access network weights (parameters)](./How-do-I-Read-Things-in-Python#load-model-and-access-network-weights-parameters)

## Load model and access network weights (parameters)

You have trained and saved a model file. Now you want to load it elsewhere and get the parameters. Here are the steps you need to follow: 

```python
    loaded_model = load_model("model_file_path")
    if is_BrainScript: 
        loaded_model = combine([loaded_model.outputs[0]])

    parameters = loaded_model.parameters
    for parameter in parameters:
        print(parameter.name, parameter.shape, "\n", parameter.value) 
```