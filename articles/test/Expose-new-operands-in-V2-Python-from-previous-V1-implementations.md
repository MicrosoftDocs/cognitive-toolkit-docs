There are several steps to expose a function that is available in the V1 library
to V2 in Python:

> Step 1: Define the Python interface

> Step 2: Define the C++ interface and plumb the interface to V1 CPP

> Step 3: Establish the glue layers on the Python and C++ API in SWIG layers

## Step 1: Python interface

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

## Step 2: Expose the operator in V2 C++ API

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

## Step 3: Updates to SWIG:

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

## Optionaly expose functionality in BrainScript V2

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
