---
title:   EvalDLL C# API
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   cpp
---

# EvalDLL C# API

There is a managed interface available for evaluating models without requiring a data reader or data file(s). This interface is implemented in CLI managed code inside the `EvalWrapper` library. This library in turn uses `Evaldll` library to perform the actual network evaluations (in native C++).

The managed interface name (in C#) is the following:  
```cs
public interface IEvaluateModelManaged<T> : IDisposable
```
Where `T` is the element type (`float` or `double`)

This interface provides the following methods:    
***
```cs
void Init(string config)
```
This method initializes the evaluation engine with the specified configuration file. The entries from the configuration file are parsed and assigned to evaluation engine, however, the network is not created inside this call. Additionally, this method will default the `numCPUThreads` property to `1`, unless the configuration file contains a different value for this property.

***

```cs
void CreateNetwork(string networkDescription)
```
This method builds the network from either the network description in the configuration file, or if the `modelPath` attribute is specified, it loads the model from disk.    
* `networkDescription` : contains the description of the network, either through a NetworkBuilder entry of a modelPath attribute.

***

```cs
void CreateNetwork(string networkDescription, List<string> outputNodeNames)
```
This method builds the network from either the network description in the configuration file, or if the `modelPath` attribute is specified, it loads the model from disk. Additionally, it replaces the list of output nodes from the configuration file, with the list passed in as an argument. This enables the caller to retrieve the output values of other nodes, such as those in the hidden layer(s).   
* `networkDescription` : contains the description of the network, either through a NetworkBuilder entry of a modelPath attribute.
* `outputNodeNames`    : list of nodes to be marked as Output, so they can be evaluated.

***

```cs
void CreateNetwork(string networkDescription, int deviceId)
```
This method builds the network from the network description in the configuration file. It is a merely a convenience method that prepends `deviceId=<deviceId>` to the `networkDescription` text.
* `networkDescription` : contains the description of the network, either through a `NetworkBuilder` property or a `modelPath` attribute.
* `deviceId`           : specifies the device Id value to prepend to the network description's `deviceId` property.

***

```cs
void CreateNetwork(string networkDescription, int deviceId, List<string> outputNodeNames)
```
This method builds the network from the network description in the configuration file. It is a merely a convenience method that prepends `deviceId=<deviceId>` to the `networkDescription` text.
* `networkDescription` : contains the description of the network, either through a NetworkBuilder entry of a modelPath attribute.
* `deviceId`           : specifies the device Id value to prepend to the network description's `deviceId` property.
* `outputNodeNames`    : list of nodes to be marked as Output, so they can be evaluated.

***

```cs
List<T> Evaluate(string outputKey, int outputSize)
```
This method evaluates the network with a single forward pass (no input) and returns the values associated with the specified layer `outputKey`.    
* `outputKey`  : layer name to return the values from.    
* `outputSize` : number of values in the output layer.  
**This method is deprecated. Instead use the `List<T> Evaluate(string outputKey)` method.**  

***

```cs
List<T> Evaluate(string outputKey)
```
This method evaluates the network with a single forward pass (no input) and returns the values associated with the specified layer `outputKey`.    
* `outputKey`  : layer name to return the values from.
Internally the method, determines the required data buffer size for the output and allocates the necessary buffer.

***

```cs
void Evaluate(Dictionary<string, List<T>> inputs, Dictionary<string, List<T>> outputs)
```
This method evaluates the network using the provided input and retrieves multiple output layers.    
* `inputs`  : the dictionary mapping input layer names to values to use as input to the network.    
* `outputs` : the dictionary mapping output layer names to values to retrieve from the network.    

***

```cs
List<T> Evaluate(Dictionary<string, List<T>> inputs, string outputKey, int outputSize)
```
This method evaluates a network with the provided input and retrieves a single output layer
* `inputs`     : the dictionary mapping input layer names to values to use as input to the network.      
* `outputKey`  : the name of the desired output layer.    
* `outputSize` : the number of values in the output layer.  
**This method is deprecated. Instead use the `List<T> Evaluate(Dictionary<string, List<T>> inputs, string outputKey)` method.**    

***

```cs
List<T> Evaluate(Dictionary<string, List<T>> inputs, string outputKey)
```
This method evaluates a network with the provided input and retrieves a single output layer
* `inputs`     : the dictionary mapping input layer names to values to use as input to the network.      
* `outputKey`  : the name of the desired output layer.    
Internally the method, determines the required data buffer size for the output and allocates the necessary buffer.

***

```cs
List<T> EvaluateRgbImage(Bitmap image, string outputKey)
```
This method evaluates a bitmap image and retrieves a single output layer
* `image`     : the bitmap image input in RGB format. It must already be re-sized to match the size expected by the network.      
* `outputKey` : the name of the desired output layer.    
Internally the method first extracts the feature vector that contains 3 channels, and then feed it as the input to the network. It also allocates the necessary buffer for the output.

***

```cs
Dictionary<string, int> GetNodeDimensions(NodeGroup nodeGroup)
```
This method returns a dictionary of items, with each item mapping the layer name (key) to the dimension size. The node group is defined through the `NodeGroup` enumeration:
```cs
    public enum class NodeGroup     
    {      
        nodeInput,  // an input node     
        nodeOutput, // an output node     
        nodeSpecified     
    };
```

***

There are two implementations of this interface available to the managed client:
```cs
class IEvaluateModelManagedF : IEvaluateModelManaged<float> // Assumes data elements of type float
```
and
```cs
class IEvaluateModelManagedD : IEvaluateModelManaged<double> // Assumes data elements of type double
```

## Example
The CSEvalClient program located [here](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/LegacyEvalDll/CSEvalClient) demonstrates the usage of this evaluation interface.
