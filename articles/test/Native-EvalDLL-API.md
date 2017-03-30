# C++ EvalDLL API

There is a native interface available for evaluating models without requiring a data reader or data file(s). This interface is implemented in C++ inside the EvalDLL.dll library in Windows and libeval.so in Linux respectively. The EvalDll.dll in turn is also used by the EvalWrapper assembly (which provides a managed layer) in Windows.

The native interface name is the following:    

    template <typename ElemType>
    class IEvaluateModel

Where `ElemType` is the element type (`float` or `double`).

This interface provides the following methods:    
***
`void Init(const std::string& config)`    
This method initializes the evaluation engine with the specified configuration file. The entries from the configuration file are parsed and assigned to evaluation engine, however, the network is not created inside this call. Additionally, this method will default the `numCPUThreads` property to `1`, unless the configuration file contains a different value for this property.

***

`void CreateNetwork(const std::string& networkDescription)`    
This method builds the network from either the network description in the configuration file, or if the `modelPath` attribute is specified, it loads the model from disk.    
* `networkDescription` : contains the description of the network, either through a NetworkBuilder entry of a modelPath attribute.

***

`void GetNodeDimensions(std::map<std::wstring, size_t>& dimensions, NodeGroup nodeGroup)`    
This method fills a dictionary of dimensions, with each fimension item mapping the layer name (key) to the dimension size. The node group is defined through the `NodeGroup` enumeration:    

    enum NodeGroup     
    {      
        nodeInput,  // an input node     
        nodeOutput, // an output node     
        nodeSpecified     
    };    
***

`void StartEvaluateMinibatchLoop(const std::wstring& outputNodeName)`    
This methods prepares the network for Evaluate calls.    
* `outputNodeName` : the name of the node that will be evaluated

***

`void Evaluate(std::map<std::wstring, std::vector<ElemType>*>& inputs, std::map<std::wstring, std::vector<ElemType>*>& outputs)`    
This method evaluates the network using the provided input and retrieves multiple output layers.    
* `inputs`  : the dictionary mapping input layer names to values to use as input to the network.    
* `outputs` : the dictionary mapping output layer names to values to retrieve from the network.    

***

`void Evaluate(std::map<std::wstring, std::vector<ElemType>*>& outputs)`    
This method evaluates the network retrieving multiple output layers. The evaluation is a single-forward pass evaluating the output nodes.
* `outputs` : the dictionary mapping output layer names to values to retrieve from the network.   

***

`void ResetState()`    
TBD

***

`void Destroy()`     
Releases resources allocated during network creation.

***

There are two implementations of this interface available to the client:    
`IEvaluateModelF : IEvaluateModel<float>`    (Assumes data elements of type `float`)    
and    
`IEvaluateModelD : IEvaluateModel<double>`    (Assumes data elements of type `double`)


## Example
The [CPPEvalClient](https://github.com/Microsoft/CNTK/tree/master/Examples/Evaluation/CPPEvalClient) program located in the folder [Examples/Evaluation/CPPEvalClient](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CPPEvalClient) demonstrates the usage of this evaluation interface.
