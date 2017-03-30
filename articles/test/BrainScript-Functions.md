Functions are BrainScript's way of creating reusable modules. Functions are parameterized expressions, and can be defined in a straight-forward manner, e.g.:

    Sqr (x) = x * x

Functions are first-class data types in the sense that functions can accept and return other functions. 

## Defining Functions
There are two ways to define functions, standard syntax and anonymous lambda syntax.

### Standard Syntax
Named functions are record members that are defined as record-member assignments of of these
two forms:

    f (arg1, arg2, ..., optionalArg1=optionalArg1Default, ...) = expression of args
    f {arg1, arg2, ..., optionalArg1=optionalArg1Default, ...} = expression of args

The two forms are syntactically identical,
but by convention, the second one (using curly braces `{ }`) form is used for functions
that return function objects with built-in learnable parameters,
such as CNTK's [predefined layers](./Layers-Referece).

The function arguments create a name scope like a record; i.e. function arguments are as if they were defined as members in an enclosing record.

#### Optional parameters
Functions can have optional parameters. These are passed by preceding their value by `name=`. If an optional parameter is not passed, its default value is used. In the function definition, optional parameters are declared using the form `name=defaultValue`.

Here is an example for a Softplus function that takes an optional steepness parameter:

    Softplus (x, steepness=1) =
        if steepness == 1                # use the simpler formula if no steepness specified
        then Log (Constant(1) + Exp (x)) # softplus(x)
        else {                           # version with steepness: 1/s softplus (s x)
            steepnessAsConstant = Constant (steepness)
            y = Reciprocal (steepnessAsConstant) .* Softplus (steepnessAsConstant .* x)
            # Note: .* denotes an elementwise product
        }.y

Note: The default values of optional parameters cannot access other parameters.
E.g., if for the above Softplus function you have a variable that defines steepness
which you want to pass to the function, just say:

    steepness = 4
    z = Softplus (x, steepness=steepness)

where the first `steepness` is the name of the parameter, and the second
is the expression for the value to pass too it.
The names will not conflict.

### Lambda Syntax
In addition to the function-definition syntax, BrainScript allows anonymous lambdas using the syntax `(x => f(x))`, which is meant to mimic C#. The following two definitions are equivalent:

    sqr(x) = x * x
    sqr = (x => x * x) 

Actually, the BrainScript parser internally transforms the latter into the former form.

The lambda syntax is currently constrained to one parameter, and also does not support optional parameters. Lambdas with more than one parameter or with optional parameters must be defined as named functions.

## Invoking Functions
Functions are invoked as you expect: By passing the arguments in parentheses; and passing optional arguments as named parameters. For example, a self-stabilizer (a scaling technique somewhat similar to batch normalization) using Softplus with steepness 4 would be written with the following two function invocations:

    beta = ParameterTensor {1, initValue=1}
    xStabilized = x .* Softplus (beta, steepness=4)

Functions are lazily executed upon invocation. Specifically, if a function defines model parameters inside (that is, the variants of `ParameterTensor{}` and any function that has calls to it inside), each invocation of the function yields an independent instance of these model parameters. Often, the result of a function invocation is then assigned to a variable or passed as a function argument. Multiple uses of such value do not result in repeated execution.

#### Purity/Referential Transparency
Although BrainScript has commonalities with functional languages, be aware that BrainScript functions are not entirely pure, in that can have one very specific side effect: that of instantiating new model parameters. For example, while the expression `2 * sqr(x)` is equivalent to `sqr(x) + sqr(x)` (with `sqr(x) = x*x`), that is not the case for `2 * ParameterTensor{N}` vs. `ParameterTensor{N} + ParameterTensor{N}`.

By convention, use round parentheses `( )` where referential transparency is given,
but curly braces `{ }` to indicate where it is not.

## Functions as Values
In BrainScript, functions are values. A named function can be assigned to a variable and passed as an argument. In fact, named functions are just record members that have the type "function." Function-name lookup is the same as record-member lookup. This allows, for example, to group functions into "namespaces" by defining them inside a record expression.

### Named Functions as Values
If a function name is used without following parentheses, it will refer to the function itself as an object.

### Partial Application/Currying
BrainScript currently does not have syntactic support partial application or currying. To achieve a similar effect, one can define a new function that calls another function with bound/captured parameters:

    Layer (x, m, n, f) = f (ParameterTensor {(m:n)} * x + ParameterTensor {n})
    Sigmoid512Layer (x) = Layer (x, 512, 512, Sigmoid)

## Examples
The following example defines a simple hierarchy of common network layer types: 

    Layers = {
        # base feed-forward without and with parameterized energy function
        # AffineLayer() defines model parameters inside.
        AffineLayer (x, m, n) = ParameterTensor {(m:n), init='heNormal'} * x
                                + ParameterTensor {n, initValue=0}
        # NLLayer applies a non-linearity on top of an affine layer
        NLLayer (x, m, n, f) = f (AffineLayer (x, m, n))
        # a few non-linearities
        SigmoidLayer (x, m, n)  = NLLayer (x, m, n, Sigmoid) # pass Sigmoid() function as the non-linaerity
        SoftplusLayer (x, m, n) = NLLayer (x, m, n, (x => Log (Constant(1) + Exp(x)))/*Softplus as lambda*/)
        ReLULayer (x, m, n)     = NLLayer (x, m, n, RectifiedLinear)
        SoftmaxLayer (x, m, n)  = NLLayer (x, m, n, Softmax)
    }

which could be used like this:

    # constants defined
    # Sample, Hidden, and Label dimensions
    SDim = 28*28 # feature dimension
    HDim = 256   # hidden dimension
    LDim = 10    # number of classes

    features = Input {SDim}
    labels   = Input {LDim}

    # layers
    h = Layers.ReLULayer (features, HDim, SDim)
    z = Layers.AffineLayer (h, LDim, HDim) # input to softmax; same as log softmax without normalization  

    # output and criteria
    P    = Softmax (z)
    ce   = CrossEntropyWithSoftmax (labels, z)
    errs = ErrorPrediction         (labels, z)

    featureNodes    = (features)
    labelNodes      = (labels)
    criterionNodes  = (ce)
    evaluationNodes = (errs)
    outputNodes     = (P)

This is the same as the initial example in [Basic Concepts](./BS-Basic-Concepts.md), but using functions.

Next: Find out about [Model Editing](./BS-Model-Editing) or go straight to [[Full Function Reference]].

## NDLNetworkBuilder (Deprecated)
Earlier versions of CNTK used the now deprecated `NDLNetworkBuilder` instead of `BrainScriptNetworkBuilder`. `NDLNetworkBuilder`'s function definition differed in a few ways. The following definition is valid in `NDLNetworkBuilder`:

    FF (X1, W1, B1) # no equal sign
    [ # both curly braces and brackets are allowed
        T = Times (W1, X1)
        FF = Plus (T, B1)
    ] # return value is FF, and not the entire record

Especially, the return value is returned via the local variable that has the same name as the function; or if no such variable is found, the variable defined last will be returned. In BrainScript this must now be written as: 

    FF (X1, W1, B1) =
    {
        T = Times (W1, X1)
        Y = Plus (T, B1)
    }.Y

Especially, the return value `Y` must be explicitly selected at the end using `.` syntax, otherwise the function's value would be the entire record. 
