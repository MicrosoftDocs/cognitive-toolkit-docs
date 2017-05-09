---
title:   How do I express things in Python
author:    chrisbasoglu
date:    04/05/2017
ms.author:   cbasoglu
ms.date:   04/05/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   python
---

# How do I express things in Python

* [Implement an attention mechanism](#implement-an-attention-mechanism)?
* [Interrogate the dimensions of internal layers of a network from within the Python API](#introspect-or-inspect-or-list-model-input-variables)?
* [Introspect or inspect or list model input variables](#introspect-or-inspect-or-list-model-input-variables)?
* [Port projection of 1D input to 1D output from Python API to C++ API](#port-projection-of-1d-input-to-1d-output-from-python-api-to-c-api)?
* [Port LSTM NDL primitives to Python](#port-lstm-ndl-primitives-to-python)?
* [Restrict a prediction to a bounded interval](#restrict-a-prediction-to-a-bounded-interval)?
* [Set the verbosity or traceLevel from Python](#set-the-verbosity-or-tracelevel-from-python)?
* [Expose new operands in V2 Python from previous V1 implementations](#expose-new-operands-in-v2-python-from-previous-v1-implementations)?

## Implement an attention mechanism

Implementing an attention mechanism requires computing a softmax over a dynamic axis. One way to do this is with a recurrence. Symbolic recurrences in Python take a little while to get used to. To make things concrete let's see how one might go about implementing a model that takes a query and a candidate answer and computes the cosine similarity of their representations. First we assume that the query and the answer have been processed by pipelines like this
```python
q_lstm = Sequential([ Embedding(500), BiRecurrence(LSTM(300), LSTM(300)), Dense(200)])
a_lstm = Sequential([ Embedding(500), BiRecurrence(LSTM(300), LSTM(300)), Dense(200)])
q_embed = q_lstm(question)
a_embed = a_lstm(answer) 
```
where `BiRecurrence` is a convenience function that you can find in the solution of the third task of [this tutorial](https://github.com/Microsoft/CNTK/blob/v2.0.rc1/Tutorials/CNTK_202_Language_Understanding.ipynb). It runs one LSTM forward, another LSTM backward and concatenates the results. After this preprocessing we have a variable-length sequence of 200 dimensional vectors for the query and another variable length sequence of 200 dimensional vectors for the answer.

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

## Interrogate the dimensions of internal layers of a network from within the Python API

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

## Introspect or inspect or list model input variables

If I create a model with some input_variable and then later from the trainer, I need the input_variable name, but I only have the model around, how can I introspect to get at the input_variables?

Say for example, you setup your trainer like this:
```python
SetupTrainer():
    input = cntk.input_variable((input_dim), np.float32)
    label = cntk.input_variable((num_output_classes), np.float32)

    z = model(input)   # (features) -> (prediction as unnormalized log prob)
    ce = cross_entropy_with_softmax(z, label)
    errs = classification_error(z, label)
    criterion = combine ([ce, errs]) # (features, labels) -> (loss, metric)

    trainer = Trainer(model, criterion.outputs[0], criterion.outputs[1], learner)

    return trainer, criterion
```

Then later, you needed to introspect to get back the names input and label by using “arguments”:
```python
# train the model
trainer, criterion = SetupTrainer()
trainer.train_minibatch({criterion.arguments[0]: features, criterion.arguments[1]: labels})  
```

## Port projection of 1D input to 1D output from Python API to C++ API

In C++ API a rank-1 tensor denotes a column and tensors are stored in column major format (i.e. axis 0 is the faster changing dimension, followed by axis 1 and so on). 

```python
input = input_variable((input_dim), np.float32)
times_param = parameter(shape=(input.shape[0], output_dim))
t = times(input, times_param)
```
So to project a 1D input of dim “inputDim” to a 1D output of dim “outputDim”, you need to setup things as follows in C++:

```cpp
input = InputVariable({ inputDim }, DataType::Float);
timesParam = CNTK::Parameter({ outputDim, input.Shape()[0] });
t = Times(timesParam, input);
```

Note how both the tensor shapes and the order of the operands to the Times operation are reversed compared to the python code. In python, to conform to the generally accepted norm established by numpy, a rank-1 tensor denoted a row vector and data layout is row major (i.e. axis 0 is the slowest changing dimension, followed by axis 1 and so on). We internally do the required shape transformations at the SWIG layer to map the python shapes and ops to the C++ implementation correctly.

## Port LSTM NDL primitives to Python

How do I find the support for following NDL LSTM primitives to Python:

**Delay**  

* How to pass argument in delay of a variable defined later in the network? E.g. for peep hole LSTM, cell state variable is defined later, but delay is needed to get t-1 cell state. Python doesn’t allow variables to be used first and defined later. 

* Ans: One needs to use a `placeholder_variable` and later a call to `replace_placeholders`. [Here](#Implement-an-attention-mechanism) is a simple example.


**RowStack**, **RowSlice** 

* Are there any substitutes for these primitives? If not how to implement them in python? Can we operate on variables as if they are numpy arrays?

* Ans: Use [splice](https://cntk.ai/pythondocs/cntk.ops.html?highlight=splice#cntk.ops.splice)
  

**DiagTime** vs **ElementTimes** 

* Is there any difference between them for vector element wise multiplication? Also is DiagTimes in supported in python?

* Use [element wise multiplication](https://cntk.ai/pythondocs/cntk.ops.html?highlight=element#cntk.ops.element_times)

**Parameter initialization** 

* How to Initialize parameters from file in python and set `computeGradient` as false.        

* Use [constants](https://cntk.ai/pythondocs/cntk.ops.html?highlight=splice#cntk.ops.splice). You can specify the initial value via a NumPy array. There are many ways to load a text (or other) file into a NumPy array.

## Restrict a prediction to a bounded interval

You can use `clip`. For example if you use the layers API
```python
z = Sequential([ Dense(500, activation=relu), 
                 Dense(4, activation=None), 
                 clip(Placeholder(), 0, 224) ])
```
will create a network with one layer with relu's and one layer with linear activations. The latter has four outputs whose predictions are limited in the interval [0,224]. This could be used to predict bounding boxes for images of size 224 x 224.

## Set the verbosity or traceLevel from Python

```python
import _cntk_py

_cntk_py.set_computation_network_trace_level(1)
```

## Expose new operands in V2 Python from previous V1 implementations

There are several steps to expose a function that is available in the V1 library
to V2 in Python:

> Step 1: Define the Python interface

> Step 2: Define the C++ interface and plumb the interface to V1 CPP

> Step 3: Establish the glue layers on the Python and C++ API in SWIG layers

### Step 1: Python interface

In this step you define the interface to the operand you want to expose. In
the following example we have and operand that takes 2 inputs that are variables
(corresponding to `x` and `y` drawn from input data) and other two user specified
inputs (`shift` and `num_negative_samples`) that are integer parameters. The
function returns a list of arrays that depend on the `num_negative_samples` and
the number of samples in a minibatch (as specified by the user when defining `x`).

*Note*: this is an exemplar and one may have different combinations of input variables,
non-learnable parameters and the structure of the returned output.

*Note2*: If you are expecting blank lines in your output see the code below
as to how one should specify blanklines. Make sure the spacing is exactly the same.
You can run `doctest` by running: `pytest __init__.py`. In `C.func_name`, C is an alias
for `import cntk as C` (refer to the `.py` file). 

> In `//bindings/python/cntk/ops` update the `__init__.py`

```
@typemap
def cosine_distance_with_negative_samples(x, y, shift, num_negative_samples, name=''):
    '''
    Give a description
    Example:
        >>> qry = # Create some data with numpy
        >>> doc = # Create some data with numpy
        >>> x = input_variable(shape=(4))
        >>> y = input_variable(shape=(4))
        >>> model = C.cosine_distance_with_negative_samples(x, y, shift=1, num_negative_samples=2)
        >>> np.round(model.eval({x: qry, y: doc}), decimals=4)
        array([[[ 1. ,  0.5,  0. ]],
        <BLANKLINE>
               [[ 1. ,  0.5,  0.5]],
        <BLANKLINE>
               [[ 1. ,  0. ,  0.5]]], dtype=float32)

    Args:
        Describe the individual variables
    Returns:
        :class:`~cntk.ops.functions.Function`
    '''
    from cntk.cntk_py import cosine_distance_with_negative_samples
    dtype = get_data_type(x, y)
    x = sanitize_input(x, dtype)
    y = sanitize_input(y, dtype)

    return cosine_distance_with_negative_samples(x, y, shift, num_negative_samples, name)
```
> Add a test in the corresponding `//bindings/python/cntk/ops/tests` directory

```
def test_cosine_distance_with_negative_samples():
  a = #Create some fake data with Numpy
  b = #Create some fake data with Numpy

  qry = input_variable(shape=(5))
  doc = input_variable(shape=(5))
  num_neg_samples = 2
  model = cosine_distance_with_negative_samples(qry, doc, shift=1, num_negative_samples=num_neg_samples)
  result = model.eval({qry:[a], doc:[b]})

  # Add tests that assert model shape and the returned values
```

### Step 2: Expose the operator in V2 C++ API

> Update `CNTKLibrary.h` (in `//Source/CNTKv2LibraryDll/API`):
Add the signature you would like to expose. This should mirror the signature
you have in the Python API

```
///
/// Create an instance of the CNTK built-in operation to compute the cosine distance
/// with negative samples for the specified input operands.
///
CNTK_API FunctionPtr CosineDistanceWithNegativeSamples(const Variable& leftOperand,
                                                       const Variable& rightOperand,
                                                       size_t shiftWindow,
                                                       size_t numberOfNegativeSamples,
                                                       const std::wstring& name = L"");
```

>  Update `CNTKLibraryInternals.h` (in `//Source/CNTKv2LibraryDll/API`): If the
V2 API signature is different from what is provided in the V1 API, you should
specify the internal API here.

```
CNTK_API FunctionPtr CosineDistanceWithNegativeSamples(const Variable& leftOperand,
                                                       const Variable& rightOperand,
                                                       const Variable& shiftWindow,
                                                       const Variable& numberOfNegativeSamples,
                                                       const std::wstring& name = L"");
```

> Update `Function.cpp` (in //Source/CNTKv2LibraryDll):
This file needs to have the API that V2 C++ API calls in the `CNTKLibary.h`. In
this instance a composite shared object is created. Note the `size_t` variable is
cast as a constant since the V1 API exposes the corresponding parameter as a
variable. We will have to remember to ensure that the V1 library function
throws an exception if the parameter passed in has dimensions larger than an
integer.

```
FunctionPtr CosineDistanceWithNegativeSamples(const Variable& leftOperand,
                                              const Variable& rightOperand,
                                              size_t shiftWindow,
                                              size_t numberOfNegativeSamples,
                                              const std::wstring& name)
{
    std::vector<Variable> operands = {leftOperand,
                                      rightOperand,
                                      Constant::Scalar((float) shiftWindow),  
                                      Constant::Scalar((float) numberOfNegativeSamples) };
    return AsComposite(MakeSharedObject<PrimitiveFunction>(
        PrimitiveOpType::CosDistanceWithNegativeSamples,
        operands, Dictionary(), name), name);
}
```

> Update `PrimitiveOpType.h` (in //Source/CNTKv2LibraryDll) : Give a Enum value
for your new operands.

```
    CosDistanceWithNegativeSamples = 67,
    // New op types should only be appended to the end of this list
```

> Update `PrimitiveFunction.h` (in //Source/CNTKv2LibraryDll): Add a string
naming the operator

```
        {PrimitiveOpType::CosDistanceWithNegativeSamples, L"CosDistanceWithNegativeSamples"},
```

or in case of the function `EditDistanceError` there are `additionalProperties` that
needs to be passed, one has to declare those properties to the `PrimitiveFunction`
class

```
        {PrimitiveOpType::EditDistanceError, L"EditDistanceError" },
```
and define the new Attributes names.
```
        static const std::wstring AttributeNameSubstitutionPenalty;
        static const std::wstring AttributeNameDeletionPenalty;
        static const std::wstring AttributeNameInsertionPenalty;
        static const std::wstring AttributeNameSquashInputs;
        static const std::wstring AttributeNameSamplesToIgnore;
```
> Update `PrimitiveOpType.cpp` (in //Source/CNTKv2LibraryDll):

In some other constructs, where the additional attributes are exposed from the
V1 API, one could have the function call be implemented as the following:

```
FunctionPtr EditDistanceError(const Variable& prediction,
                              const Variable& labels,
                              float subPen, float delPen, float insPen,
                              bool squashInputs,
                              const vector<size_t>& samplesToIgnore,
                              const std::wstring& name)
{
    auto additionalProperties = Dictionary();
    additionalProperties[PrimitiveFunction::AttributeNameSubstitutionPenalty] = subPen;
    additionalProperties[PrimitiveFunction::AttributeNameDeletionPenalty] = delPen;
    additionalProperties[PrimitiveFunction::AttributeNameInsertionPenalty] = insPen;
    additionalProperties[PrimitiveFunction::AttributeNameSquashInputs] = squashInputs;
    additionalProperties[PrimitiveFunction::AttributeNameSamplesToIgnore] = AsDictionaryValueVector(samplesToIgnore);

    return BinaryOp(PrimitiveOpType::EditDistanceError, prediction, labels, std::move(additionalProperties), name);
}
```

> Update the `CompositeFunction.cpp` (in //Source/CNTKv2LibraryDll): Here constructs
the `CosDistanceWithNegativeSamplesNode` object that holds the implementation of
the function one would like to expose.

```
case PrimitiveOpType::CosDistanceWithNegativeSamples:
    computationNodePtr = New<CosDistanceWithNegativeSamplesNode<ElementType>>(network->GetDeviceId(), internalNodeName);
```

In another example, where parameters are passed in directly from the caller the
operand implementation would look like

```
case PrimitiveOpType::EditDistanceError:
{
    auto subPen = functionConfig[PrimitiveFunction::AttributeNameSubstitutionPenalty].Value<float>();
    auto delPen = functionConfig[PrimitiveFunction::AttributeNameDeletionPenalty].Value<float>();
    auto insPen = functionConfig[PrimitiveFunction::AttributeNameInsertionPenalty].Value<float>();
    auto squashInputs = functionConfig[PrimitiveFunction::AttributeNameSquashInputs].Value<bool>();
    auto samplesToIgnore = AsVector<size_t>(functionConfig[PrimitiveFunction::AttributeNameSamplesToIgnore].Value<std::vector<DictionaryValue>>());
    computationNodePtr = New<EditDistanceErrorNode<ElementType>>(network->GetDeviceId(), subPen, delPen, insPen, squashInputs, samplesToIgnore, internalNodeName);
    break;
}
```

> Update `BackCompat.cpp` (in //Source/CNTKv2LibraryDll): Depending on how the
function parameters were pass the change could be as simple as:

```
else if (node->OperationName() == OperationNameOf(CosDistanceWithNegativeSamplesNode))
{
    opType = PrimitiveOpType::CosDistanceWithNegativeSamples;
}
```

or if additional parameters are to be passed in then the following snippet is more
appropriate

```
else if (node->OperationName() == OperationNameOf(EditDistanceErrorNode))
{
    auto edNode = node->As<EditDistanceErrorNode<ElementType>>();
    primitiveFunctionConfigParameters[PrimitiveFunction::AttributeNameInsertionPenalty] = edNode->InsertionPenalty();
    primitiveFunctionConfigParameters[PrimitiveFunction::AttributeNameDeletionPenalty] = edNode->DeletionPenalty();
    primitiveFunctionConfigParameters[PrimitiveFunction::AttributeNameSubstitutionPenalty] = edNode->SubstitutionPenalty();
    primitiveFunctionConfigParameters[PrimitiveFunction::AttributeNameSquashInputs] = edNode->SquashInputs();
    primitiveFunctionConfigParameters[PrimitiveFunction::AttributeNameSamplesToIgnore] = AsDictionaryValueVector(edNode->SamplesToIgnore());

    opType = PrimitiveOpType::EditDistanceError;
}
```

> Possible updates to V1 operator signature. In the case of the CosineDistanceWithNegativeSamples
we cast a int exposed in the API to a Variable type exposed in the V1 library. Hence,
validation is needed for those two parameters. The implementation of the V1
functionality is in `LinearAlgebraNodes.h` (in //Source/ComputationNetworkLib):
We validate that the shape of the provided parameter as expected is a Constant scalar.

```
auto input3AsLearnableParameterNode = Input(3)->template As<LearnableParameter<ElemType>>();
if (isFinalValidationPass && (!input3AsLearnableParameterNode || input3AsLearnableParameterNode->GetLearningRateMultiplier() != 0) || (Input(3)->GetSampleLayout().GetNumElements() != 1))
    LogicError("%ls %ls operation expects a constant scalar for Input(3) which corresponds to number of negative samples.", NodeName().c_str(), OperationName().c_str());

```
For other functions where additional parameters are passed from the python API one
need to modify the construtor of the implementation source. For example to expose
`EditDistanceError` where penalties are passed into the construtor some of the
code changes are as shown below.

```
EditDistanceErrorNode(DEVICEID_TYPE deviceId, float subPen, float delPen, float insPen, bool squashInputs, std::vector<size_t> samplesToIgnore, const wstring & name)
    : Base(deviceId, name), m_subPen(subPen), m_delPen(delPen), m_insPen(insPen), m_squashInputs(squashInputs), m_SamplesToIgnore(samplesToIgnore)
{
}

EditDistanceErrorNode(const ScriptableObjects::IConfigRecordPtr configp)
    : EditDistanceErrorNode(configp->Get(L"deviceId"), configp->Get(L"subPen"), configp->Get(L"delPen"), configp->Get(L"insPen"), configp->Get(L"squashInputs"), configp->Get(L"samplesToIgnore"), L"<placeholder>")
{
    AttachInputsFromConfig(configp, this->GetExpectedNumInputs());
}

```
Some additional plumbing of the passed variables into local values and explicit
copying into individual nodes may be needed.

> Update `SeriealizationTest.cpp` (in //Tests/UnitTests/V2LibararyTests): Check
for the unique ID assertions

```
     static_cast<size_t>(PrimitiveOpType::CosDistanceWithNegativeSamples) == 67,

```

### Step 3: Updates to SWIG:

Some of the compiler warnings are to be ignored.

> Update the `cntk_py.i` (in //bindings/python/cntk):

```
%ignore CNTK::Internal::CosineDistanceWithNegativeSamples;
```

> Update the `cntk_cs.i` (in //bindings/csharp/Swig):

```
%ignore CNTK::Internal::CosineDistanceWithNegativeSamples;
%ignore_function CNTK::Internal::CosineDistanceWithNegativeSamples;
```

### Optionaly expose functionality in BrainScript V2

> Update `ComputationNetworkBuilder.h` (in //Source/ComputationNetworkLib):

```
    ComputationNodePtr EditDistanceError(const ComputationNodePtr a, const ComputationNodePtr b, float subPen, float delPen, float insPen, bool squashInputs, vector<size_t> samplesToIgnore, const std::wstring nodeName = L"");
```
and create a new node and attach inputs
```
    else if (nodeType == OperationNameOf(EditDistanceErrorNode))                
       return     New<EditDistanceErrorNode<ElemType>>(forward<_Types>(_Args)...);

    template <class ElemType>
    shared_ptr<ComputationNode<ElemType>> ComputationNetworkBuilder<ElemType>::EditDistanceError(const ComputationNodePtr a, const ComputationNodePtr b, float subPen, float delPen, float insPen, bool squashInputs, vector<size_t> samplesToIgnore, const std::wstring nodeName)
    {
        return net.AddNodeToNetAndAttachInputs(New<EditDistanceErrorNode<ElemType>>(net.GetDeviceId(), subPen, delPen, insPen, squashInputs, samplesToIgnore, nodeName), { a, b });
    }

```
