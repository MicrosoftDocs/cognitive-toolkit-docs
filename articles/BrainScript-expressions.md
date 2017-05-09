---
title:   BrainScript Expressions
author:    chrisbasoglu
date:    03/10/2017
ms.author:   cbasoglu
ms.date:   03/10/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript Expressions

This section is the specification of BrainScript expressions, although we intentionally use informal language to keep it readable and accessible. Its counterpart is the specification of BrainScript function-definition syntax, which can be found [here](./BrainScript-Functions.md).

Every brain script is an expression, which in turn consists of expressions assigned to record member variables. The outermost level of a network description is an implied record expression. BrainScript has the following kinds of expressions:

* literals like numbers and strings
* math-like infix and unary operations like [`a + b`](./Binary-Operations.md)
* a ternary conditional expression
* function invocations
* records, record-member accesses
* arrays, array-element accesses
* function expressions (lambdas)
* built-in C++ object construction

We intentionally kept the syntax for each of these as close as possible to popular languages,
so much of what you find below will look very familiar.

## Concepts
Before describing the individual kinds of expression, first some basic concepts.

### Immediate vs. Deferred Computation
BrainScript knows two kinds of values: *immediate* and *deferred*. Immediate values are computed during the processing of BrainScript, while deferred values are objects that represent nodes in the computation network. The computation network describes the actual computation performed by the CNTK execution engine during training and use of the model.

Immediate values in BrainScript are meant to parameterize the computation. They denote tensor dimensions, the number of network layers, a pathname to load a model from, etc. Since BrainScript variables are immutable, immediate values are always constants.

Deferred values arise from the primary purpose of brain scripts: to describe the computation network. The computation network can be seen as a function that is passed to the training or inference routine, which then executes the network function via the CNTK execution engine. Hence, the result of many BrainScript expressions is a computation node in a computation network, rather than an actual value. From the viewpoint of BrainScript, a deferred value is a C++ object of type `ComputationNode` that represents a network node. For example, taking the sum of two network nodes creates a new network node that represents the summation operation which takes the two nodes as inputs.

### Scalars vs. Matrices vs. Tensors
All values in the computation network are numeric *n*-dimensional arrays which we call *tensors*, and *n* denotes the *tensor rank*. Tensor dimensions are explicitly specified for inputs and model parameters; and automatically inferred by operators.

The most common data type for computation, *matrices*, are just tensors of rank 2. Column vectors are tensors of rank 1, while row vectors are rank 2. The matrix product is a common operation in neural networks.

Tensors are always deferred values, that is, objects in the deferred computation graph. Any operation that involves a matrix or tensor becomes part of the computation graph and is evaluated during training and inference. Tensor *dimensions*, however, are inferred/checked upfront at BS processing time.

Scalars may be either immediate or deferred values. Scalars that parameterize the computation network itself, such as tensor dimensions, must be immediate, i.e. computable at the time of processing the BrainScript. Deferred scalars are rank-1 tensors of dimension `[1]`. They are part of the network itself, including learnable scalar parameters such as self-stabilizers, and constants as in `Log (Constant (1) + Exp(x))`.

### Dynamic Typing
BrainScript is a dynamically-typed language with an extremely simple type system. Types are checked during processing of BrainScript when a value is used.

Immediate values are of type number, Boolean, string, record, array, function/lambda, or one of CNTK's predefined C++ classes. Their types are checked at time of use (for example, the `COND` argument to the `if` statement is verified to be a `Boolean`, and an array element access requires the object to be an array).

All deferred values are tensors. Tensor dimensions are part of their type, which are checked or inferred during processing of BrainScript.

Expressions between an immediate scalar and a deferred tensor must explicitly convert the scalar into a deferred `Constant()`. For example, the Softplus non-linearity must be written as `Log (Constant(1) + Exp (x))`. (It is planned to remove this requirement in an upcoming update.)

## Expression Types

### Literals
Literals are numeric, Boolean, or string constants, as you would expect. Examples:
* `13`, `42`, `3.1415926538`, `1e30`
* `true`, `false`
* `"my_model.dnn"`, `'single quotes'`

Numeric literals are always double-precision floating point. There is no explicit integer type in BrainScript, although some expressions such as array indices will fail with an error if presented values that are not integers.

String literals may use single or double quotes, but have no way of escaping quotes or other characters inside (string containing both single and double quotes must be computed, e.g. `"He'd say " + '"Yes!" in a jiffy.'`). String literals can span multiple lines; for example:

    I3 = Parameter (3, 3, init='fromLiteral', initFromLiteral = '1 0 0
                                                                 0 1 0
                                                                 0 0 1')

### Infix and Unary Operations
BrainScript supports the operators given below. BrainScript operators are chosen to mean what one would expect coming from popular languages, with the exception of `.*` (element-wise product), `*` (matrix product), and special broadcasting semantics of element-wise operations.

#### Numeric [Infix Operators](./Binary-Operations.md) `+`, `-`, `*`, `/`, `.*`
* `+`, `-`, and `*` apply to scalars, matrices, and tensors.
* `.*` denotes an element-wise product. Note for Python users: This is the equivalent of numpy's `*`.
* `/` is only supported for scalars. An element-wise division can be written using built-in `Reciprocal(x)` computes an element-wise `1/x`.

#### Boolean Infix Operators `&&`, `||`
These denote Boolean AND and OR, respectively.

#### String Concatenation (`+`)
Strings are concatenated with `+`. Example: `BS.Networks.Load (dir + "/model.dnn")`.

#### Comparison Operators
The six comparison operators are `<`, `==`, `>`, and their negations `>=`, `!=`, `<=`. They can be applied to all immediate values as expected; their result is a Boolean.

To apply comparison operators to tensors, one must use built-in functions instead, such as [`Greater()`](./Binary-Operations.md).

#### [Unary](./Unary-Operations.md) `-`, `!`
These denote negation and logical negation, respectively. `!` can currently only be used for scalars.

#### [Elementwise Operations](./Binary-Operations.md) and [Broadcasting Semantics](./Binary-Operations.md#broadcasting-semantics)
When applied to matrices/tensors, `+`, `-`, and `.*` are applied element-wise.

All element-wise operations support [*broadcasting semantics*](./Binary-Operations.md#broadcasting-semantics).
Broadcasting means that any dimension that is specified as 1 will automatically repeated to match any dimension.

For example, a row vector of dimension `[1 x N]` can directly be added to a matrix of dimension `[M x N]`. The `1` dimension will automatically repeated `M` times. Further, tensor dimensions are automatically padded with `1` dimensions. For example, it is allowed to add a column vector of dimension `[M]` a `[M x N]` matrix. In this case, the column vector's dimensions are automatically padded to `[M x 1]` to match the rank of the matrix.

Note to Python users: Unlike numpy, broadcasting dimensions are left-aligned.

#### The [Matrix-Product Operator](./Times-And-TransposeTimes.md) `*`
The `A * B` operation denotes the matrix product. It can also be applied to sparse matrices, which improves efficiency for handling text inputs or labels that are represented as one-hot vectors. In CNTK, the matrix product has an extended interpretation that allows it to be used with tensors of rank > 2. It is, for example, possible to multiply every column in a rank-3 tensor individually with a matrix.

The matrix product and its tensor extension are described in detail [here](./Times-and-TransposeTimes.md).

Note: To multiply with a scalar, use the element-wise product `.*`.

Python users be advised that `numpy` uses the `*` operator for the *element-wise* product, not the matrix product. CNTK's `*` operator corresponds to `numpy`'s `dot()`, while CNTK's equivalent to Python's `*` operator for `numpy` arrays is `.*`.

#### The Conditional Operator `if`
Conditionals in BrainScript are expressions, like the C++ `?` operator. The BrainScript syntax is `if COND then TVAL else EVAL`, where `COND` must be an immediate Boolean expression, and the result of the expression is `TVAL` if `COND` is true, and `EVAL` otherwise. The `if` expression is useful for implementing multiple similar flag-parameterized configurations in the same BrainScript, and also for recursion.

(The `if` operator only works for immediate scalar values. To implement conditionals for deferred objects, use the built-in function [`BS.Boolean.If()`](./If-Operation.md), which allows to select a value from one of two tensors based on a flag tensor. It has the form `If (cond, tval, eval)`.)

#### Function Invocations
BrainScript has three kinds of [functions](./BrainScript-Functions.md): built-in primitives (with C++ implementations), library functions (written in BrainScript), and user-defined (BrainScript). Examples of built-in functions are `Sigmoid()` and `MaxPooling()`. Library and user-defined functions are mechanically the same, just saved in different source files. All kinds are invoked, similarly to math and common languages, using the form `f (arg1, arg2, ...)`.

Some functions accept optional parameters. Optional parameters are passed as named parameters, for example `f (arg1, arg2, option1=..., option2=...)`.

Functions can be invoked recursively, for example:

    DNNLayerStack (x, numLayers) =
        if numLayers == 1
        then DNNLayer (x, hiddenDim, featDim)
        else DNNLayer (DNNLayerStack (x, numLayers-1), # add a layer to a stack of numLayers-1
                       hiddenDim, hiddenDim)

Note how the `if` operator is used to end the recursion.

#### Layer Creation
Functions can create entire layers or models that are function objects which behave like functions as well.
By convention, a function that creates a layer with learnable parameters
uses curly braces `{ }` instead of parentheses `( )`.
You will encounter expressions like this:

    h = DenseLayer {1024} (v)

Here, two invocations are at play. The first, `DenseLayer{1024}`, is a function call that creates
a function object, which then in turn is applied to data `(v)`.
Since `DenseLayer{}` returns a function object with learnable parameters,
it uses `{ }` to denote this.

#### Records and Record-Member Access
Record expressions are assignments surrounded by curly braces. For example:

    {
        x = 13
        y = x * factorParameter
        f (z) = y + z
    }

This expression defines a record with three members, `x`, `y`, and `f`, where `f` is a function.
Inside the record, expressions can reference other record members just by their name, like `x` is accessed above in the assignment of `y`.

Unlike many languages however, record entries can be declared in *any order*. E.g. `x` could be declared after `y`. This is to facilitate definition of recurrent networks. Any record member is accessible from any other record member's expression. This is different from, say, Python; and similar to F#'s `let rec`. Cyclic references are forbidden, with the special exception of the `PastValue()` and `FutureValue()` operations.

When records are nested (record expressions used inside other records), then record members are looked up through the entire hierarchy of enclosing scopes. In fact, *every* variable assignment is part of a record: The outer level of a BrainScript is an implied record as well. In the above example, `factorParameter` would have to be assigned as a record member of an enclosing scope.

Functions assigned inside a record will capture record members they reference. For example, `f()` will capture `y`, which in turn depends on `x` and the externally defined `factorParameter`. Capturing these means that `f()` can be passed as a lambda into outside scopes that do not contain `factorParameter` or have access to it.

From outside, record members are accessed using the `.` operator. E.g. if we had assigned the above record expression to a variable `r`, then `r.x` would yield the value `13`. The `.` operator does not traverse enclosing scopes: `r.factorParameter` would fail with an error.

(Note that until CNTK 1.6, instead of curly braces `{ ... }`, records used brackets `[ ... ]`.
This is still allowed, but deprecated.)

#### Arrays and Array Access
BrainScript has a one-dimensional array type for immediate values (not to be confused with tensors). Arrays are indexed using `[index]`. Multi-dimensional arrays can be emulated as arrays of arrays.

Arrays of at least 2 elements can be declared using the `:` operator. For example, the following declares a 3-dimensional array named `imageDims` which then gets passed to `ParameterTensor{}` to declare a rank-3 parameter tensor:

    imageDims = (256 : 256 : 3)
    inputFilter = ParameterTensor {imageDims}

It is also possible to declare arrays whose values reference each other. For this, one must use the somewhat more involved array assignment syntax:

    arr[i:i0..i1] = f(i)

which constructs an array named `arr` with lower index bound `i0` and upper index bound `i1`, `i` denoting the variable to denote the index variable in the *initializer expression* `f(i)`, which in turn denotes the value of `arr[i]`. The values of the array are evaluated lazily. That allows the initializer expression for a specific index `i` to access other elements `arr[j]` of the same array, as long as there is no cyclic dependency. For example, this can be used to declare a stack of network layers:

    layers[l:1..L] =
        if l == 1
        then DNNLayer (x, hiddenDim, featDim)
        else DNNLayer (layers[l-1], hiddenDim, hiddenDim)

Unlike the recursive version of this that we introduced earlier, this version preserves access to each individual layer by saying `layers[i]`.

Alternatively, there is also an expression syntax `array[i0..i1] (i => f(i))`, which is less convenient but sometimes helpful. The above would look like this:

    layers = array[1..L] (l =>
        if l == 1
        then DNNLayer (x, hiddenDim, featDim)
        else DNNLayer (layers[l-1], hiddenDim, hiddenDim)
    )

Note: Currently there is no way to declare an array of 0 elements. This will be addressed in a future version of CNTK.

#### Functions Expressions and Lambdas
In BrainScript, functions are values. A named function can be assigned to a variable and passed as an argument, e.g.:

    Layer (x, m, n, f) = f (ParameterTensor {(m:n)} * x + ParameterTensor {n})
    h = Layer (x, 512, 40, Sigmoid)

where `Sigmoid` is passed as a function which is used inside `Layer()`. Alternatively, a C#-like lambda syntax `(x => f(x))` allows to create anonymous functions inline. For example, this defines a network layer with a Softplus activation:

    h = Layer (x, 512, 40, (x => Log (Constant(1) + Exp (x)))

The lambda syntax is currently limited to functions with a single parameter.

#### Layer pattern
The above `Layer()` example combines parameter creation and function application.
A preferred pattern is to separate these into two steps:
* create parameters and returns a function object that holds these parameters
* create the function that applies the parameters to an input

Specifically, the latter is a member of the function object as well.
The above example could be rewritten as:

    Layer {m, n, f} = {
        W = ParameterTensor {(m:n)}  # parameter creation
        b = ParameterTensor {n}
        apply (x) = f (W * x + b)    # the function to apply to data
    }.apply

and would be invoked as:

    h = Layer {512, 40, Sigmoid} (x)

The reason for this pattern is that typical network types consist of
applying one function after another to an input,
which can be written more easily using the [`Sequential()`](./Sequential.md) function.

CNTK comes with a rich set of predefined layers, which are [described here](./BrainScript-Layers-Reference.md).

#### Constructing Built-in C++ CNTK Objects
Ultimately, all BrainScript values are C++ objects. The special BrainScript operator `new` is used for interfacing with the underlying CNTK C++ objects. It has the form `new TYPE ARGRECORD` where `TYPE` is one of a hard-coded set of the predefined C++ objects exposed to BrainScript, and `ARGRECORD` is a record expression that is passed to the C++ constructor.

You will likely ever only get to see this form if you are using the parenthesis form of `BrainScriptNetworkBuilder`, i.e. `BrainScriptNetworkBuilder = (new ComputationNetwork { ... })`, as [described here](./BrainScript-Network-Builder.md).
But now you know what it means: `new ComputationNetwork` creates a new C++ object of type `ComputationNetwork`, where `{ ... }` simply defines a record that is passed to the C++ constructor of the internal `ComputationNetwork` C++ object, which will then look for 5 specific members `featureNodes`, `labelNodes`, `criterionNodes`, `evaluationNodes`, and `outputNodes`, as [explained here](./BrainScript-Basic-Concepts.md#summary-of-special-names).

Under the hood, all built-in functions are really `new` expressions that construct objects of the CNTK C++ class `ComputationNode`. For illustration, see how the `Tanh()` built-in is actually defined as creating a C++ object:

   Tanh (z, tag='') = new ComputationNode { operation = 'Tanh' ; inputs = z /*plus the function args*/ }

### Expression Evaluation Semantics
BrainScript expressions are evaluated upon first use. Since the primary purpose of BrainScript is to describe the network, the value of an expression is often a node in a computation graph for deferred computation. For example, from the BrainScript angle, `W1 * r + b1` in the example above 'evaluates' to a `ComputationNode` object rather than a numerical value; whereas the actual numerical values involved will get computed by the graph execution engine. Only BrainScript expressions of scalars (e.g. `28*28`) are 'computed' at the time the BrainScript is parsed. Expressions that are never used (e.g. due to a condition) are never evaluated (nor checked for type errors).

### Common Usage Patterns of Expressions
Below are some common patterns used with BrainScript.

#### Namespaces for Functions
By grouping function assignments into records, one can achieve a form of namespacing. For example:

    Layers = {
        Affine (x, m, n) = ParameterTensor {(m:n)} * x + ParameterTensor {n}
        Sigmoid (x, m, n) = Sigmoid (Affine (x, m, n))
        ReLU (x, m, n) = RectifiedLinear (Affine (x, m, n))
    }
    # 1-hidden layer MLP
    ce = CrossEntropyWithSoftmax (Layers.Affine (Layers.Sigmoid (feat, 512, 40), 9000, 512))

#### Locally scoped variables
Sometimes it is desirable to have locally scoped variables and/or functions for more complex expressions. This can be achieved by enclosing the entire expression in a record, and immediately accessing its result value. For example:

    { x = 13 ; y = x * x }.y

will create a 'temporary' record with a member `y` which is immediately read out. This record is 'temporary' since it is not assigned to a variable, and thus its members are not accessible except for `y`.

This pattern is often used for making NN layers with built-in parameters more readable, for example:

    SigmoidLayer (m, n, x) = {
        W = Parameter (m, n, init='uniform')
        b = Parameter (m, 1, init='value', initValue=0)
        h = Sigmoid (W * x + b)
    }.h

Here, `h` can be thought of the 'return value' of this function.

Next: Learn about [Defining BrainScript Functions](./BrainScript-Functions.md)

### NDLNetworkBuilder (Deprecated)
Earlier versions of CNTK used the now deprecated `NDLNetworkBuilder` instead of `BrainScriptNetworkBuilder`. `NDLNetworkBuilder` implemented a much reduced version of BrainScript. It had the following restrictions:

* No infix syntax. All operators must be invoked through function invocations. E.g. `Plus (Times (W1, r), b1)` instead of `W1 * r + b1`.
* No nested record expressions. There is only one implied outer record.
* No conditional expression or recursive function invocation.
* User-defined functions must be declared in special `load` blocks and cannot nest.
* The last record assignment is automatically used as the value of a function.
* The `NDLNetworkBuilder` language version is not Turing-complete.

`NDLNetworkBuilder` should not be used anymore.
