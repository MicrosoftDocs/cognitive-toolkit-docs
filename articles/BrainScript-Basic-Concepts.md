---
title:   BrainScript Basic Concepts
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/10/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# BrainScript Basic Concepts

## BrainScript--A Walk-Through
This section introduces the basic concepts of the "BrainScript" language. A new language? Don't worry & read on, it's very straight-forward.

In CNTK, custom networks are defined using the [`BrainScriptNetworkBuilder`](./BrainScript-Network-Builder.md) and described in the CNTK network description language "BrainScript." Likewise, networks descriptions are called brain scripts.

BrainScript provides a simple way to define a network in a code-like fashion, using expressions, variables, primitive and self-defined functions, nested blocks, and other well understood concepts. It looks similar to a scripting language in syntax.

OK, let's get our feet wet with a complete BrainScript example!

### A Complete BrainScript Example Network Definition
The following example shows the network description of a simple Neural Network with one hidden layer and one classification layer. We will explain concepts along this example.
Before you move on, maybe spend a few minutes with the example and try to guess what it means.
You may find, as you read on, that you guessed most of it correctly.

    BrainScriptNetworkBuilder = {   # (we are inside the train section of the CNTK config file)

        SDim = 28*28 # feature dimension
        HDim = 256   # hidden dimension
        LDim = 10    # number of classes

        # define the model function. We choose to name it 'model()'.
        model (features) = {
            # model parameters
            W0 = ParameterTensor {(HDim:SDim)} ; b0 = ParameterTensor {HDim}
            W1 = ParameterTensor {(LDim:HDim)} ; b1 = ParameterTensor {LDim}

            # model formula
            r = RectifiedLinear (W0 * features + b0) # hidden layer
            z = W1 * r + b1                          # unnormalized softmax
        }.z

        # define inputs
        features = Input {SDim}
        labels   = Input {LDim} 

        # apply model to features
        z = model (features)

        # define criteria and output(s)
        ce   = CrossEntropyWithSoftmax (labels, z)  # criterion (loss)
        errs = ErrorPrediction         (labels, z)  # additional metric
        P    = Softmax (z)     # actual model usage uses this

        # connect to the system. These five variables must be named exactly like this.
        featureNodes    = (features)
        inputNodes      = (labels)
        criterionNodes  = (ce)
        evaluationNodes = (errs)
        outputNodes     = (P)
    }

### BrainScript Syntax Basics
Before we dig right in, a few general notes about BrainScript's syntax.

BrainScript uses a simple syntax that is aimed at allowing to express neural networks in a way that looks like math formulas.
Hence, the fundamental syntactical unit is the assignment, which is used in both variable assignments and function definitions. For example:

    Softplus (x) = Log (1 + Exp (x))
    h = Softplus (W * v + b)

#### Lines, Comments, Include
Although an assignment is typically written on a single line, expressions can span multiple lines. To put multiple assignments on a single line, however, you must separate them by a semicolon. For example:

    SDim = 28*28 ; HDim = 256 ; LDim = 10    # feature, hidden, and label dimension

Other than requiring a semicolon between assignments in absence of a line break, BrainScript is not white-space sensitive.

BrainScript understands line-end comments using both Python style `#` and C++-style `//`. Inline comments use C syntax (`/* this is a comment*/`), but unlike C, these may not span multiple lines.

For BrainScript embedded in CNTK config files (as opposed to BrainScript read from a separate file through an `include` directive), due to an interaction with the config parser, there is the (somewhat strange) additional restriction that any parentheses, curly braces, or brackets must be balanced inside C/C++ style comments and string literals. Hence, no smileys in C/C++-style comments!

An `include "PATH"` directive may be used at any place to insert the content of a file at the point of the statement. Here, `PATH` can be an absolute or a relative relative path (with or without subdirectories). If it is a relative path, the following locations are searched in order: current working directory; directory(s) containing outer including files if any; directory(s) containing the config file(s); and lastly the directory containing the CNTK executable. All built-in BrainScript functions are included this way from a file called `CNTK.core.bs` which is located next to the CNTK executable.

#### Expressions
Next, you need to know about BrainScript expressions--the formulas that describe your network.
[BrainScript expressions](./BrainScript-Expressions.md) 
are written math-like in a syntax similar to popular programming languages. The simplest expressions are literals, e.g. numbers and strings. A math-like example is `W1 * r + b`, where `*` refers to a scalar, matrix, or tensor product depending on the type of the variables. Another common kind of expression is the function invocation, e.g. `RectifiedLinear (.)`.

BrainScript is a dynamically-typed language. An important expression type is the *record*, defined using the `{...}` syntax, and accessed through the dot syntax. For example, `r = { x = 13 ; y = 42 }` assigns a record with two members to `r`, where the first member can be accessed as `r.x`.

In addition to the usual math operators, BrainScript has a conditional expression (`if c then t else f`), an array expression, and simple lambdas. Lastly, to interface with the CNTK C++ code, BrainScript can directly instantiate a limited set of predefined C++ objects, predominantly the `ComputationNode` that computational networks are composed of. For more details, see [Expressions](./BrainScript-Expressions.md).

BrainScript expressions are evaluated upon first use. The primary purpose of BrainScript is to describe the network, so the value of an expression is often not a final value, but rather a node in a computation graph for deferred computation (as in `W1 * r + b`). Only BrainScript expressions of scalars (e.g. `28*28`) are 'computed' at the time the BrainScript is parsed. Expressions that are never used (e.g. due to a condition) are never evaluated.

Note: The now deprecated `NDLNetworkBuilder` version only supported function-invocation syntax; e.g., one would have to write `Plus (Times (W1, r), b1)`.

#### Variables
Variables can hold the value of any BrainScript expression (number, string, record, array, lambda, CNTK C++ object), and are substituted when used in an expression. Variables are immutable, i.e. assigned only once. For example, the network definition above begins with:

    SDim = 28*28  
    HDim = 256
    LDim = 10

Here the *variables* are set to scalar numeric values that are used as parameters in subsequent expressions. These values are the dimensions of the data samples, hidden layers, and labels used in training. This particular example setup is for the [MNIST dataset](http://yann.lecun.com/exdb/mnist/), which is a collection of `[28 x 28]`-pixel images. Each image is a handwritten digit (0-9), so there are 10 possible labels that can be applied to each image. The hidden activation dimension `HDim` is a user choice.

Most variables are record members (the outer BrainScript block is implicitly a record). In addition, variables can be function arguments or stored as arrays elements.

OK, ready to go through the model definition.

### Defining the Network
A network is primarily described by formulae of how the network's outputs are computed from the inputs. We call this the *model function*, which is often defined as an actual function in BrainScript. As part of the model function, the user must declare the *model parameters*. Lastly, one must define the network's *inputs*, and *criteria/outputs*. All of these are defined as variables. The input and criteria/output variables must then be communicated to the system.

#### The Network's Model Function and Model Parameters
The *model function* contains the actual network formulas and the respective model parameters. Our example uses matrix product and addition, and the 'primitive' (built-in) function for the energy function `RectifiedLinear()`, so the core of the network function consists of these equations:

    r = RectifiedLinear (W0 * features + b0)
    z = W1 * r + b1 

*Model parameters* are matrices, bias vectors, or any other tensor that constitute the learned model upon completion of training. The model-parameter tensors are used in transforming the input sample data into the desired output, and are updated by the learning process. The example network above contains the following matrix parameters:

    W0 = ParameterTensor {(HDim:SDim)}
    b0 = ParameterTensor {(HDim)}

In this case, `W0` is the weight matrix and `b0` is the bias vector. [`ParameterTensor{}`](./Parameters-And-Constants.md#parametertensor) denotes a special CNTK primitive, which instantiates a vector, matrix, or arbitrary-rank tensor, and takes the dimension parameters as a BrainScript
array (numbers concatenated by a colon `:`). A vector's dimension is a single number, while
a matrix dimension should be specified as `(numRows:numCols)`. By default, Parameters are initialized with uniform random numbers when instantiated directly, and `heNormal` when used through [layers](./BrainScript-Layers-Reference.md),
but other options exist ([see here](./Parameters-And-Constants.md#random-initialization)) for the full list.
Unlike regular functions, `ParameterTensor{}` takes its arguments
in curly braces instead of parentheses. Curly braces are the BrainScript convention
for functions that create parameters or objects, as opposed to functions.

This is then all wrapped into a BrainScript function. BrainScript functions are declared in the form `f(x) = an expression of x`. For example, `Sqr (x) = x * x` is a valid BrainScript function declaration. Could not be much simpler and direct, right?

Now, the actual model function of our example above is a bit more complex:

    model (features) = {
        # model parameters
        W0 = ParameterTensor {(HDim:SDim)} ; b0 = ParameterTensor {HDim}  
        W1 = ParameterTensor {(LDim:HDim)} ; b1 = ParameterTensor {LDim}

        # model formula
        r = RectifiedLinear (W0 * features + b0) # hidden layer
        z = W1 * r + b1                          # unnormalized softmax
    }.z

The outer `{ ... }` and that final `.z` deserves some explanation. The outer curlies `{ ... }` and their content actually define a record with 6 record members (`W0`, `b0`, `W1`, `b1`, `r`, and `z`). However, the model function's value is just `z`; all others are internal to the function. Hence, we use `.z` to select the record member we care to return. This is just the dot syntax for accessing record members. This way, the other record members are not accessible from outside. But they continue to exist as part of the expression to compute `z`.
The `{ ... ; x = ... }.x` pattern is a way of using local variables.

Note that the record syntax is not necessary. Alternatively, `model(features)` could also have been declared without the detour via the record, as a single expression:

    model (features) = ParameterTensor {(LDim:HDim)} * (RectifiedLinear (ParameterTensor {(HDim:SDim)}
                       * features + ParameterTensor {HDim})) + ParameterTensor {LDim}

This is much harder to read, and, more importantly, will not allow to use the same parameter at multiple places in the formula.

#### Inputs
*Inputs* into the network are defined by the sample data and the labels associated with the samples:

    features = Input {SDim}
    labels   = Input {LDim}

`Input{}` is the second special CNTK primitive needed for model definition (the first being `Parameter{}`). It creates a variable that receives input from outside the network: from the reader. The argument of `Input{}` is the data dimension. In this example, the `features` input will have the dimensions of the sample data (which we defined in variable `SDim`), and the `labels` input will have the dimensions of the labels. The variable names of the inputs are expected to match corresponding entries in the reader definition.

#### Training Criteria and Network Outputs
We still need to declare how the network's output interacts with the world.
Our model function computes logit values (non-normalized log probabilities).
These logit values can be used to

* define the training criterion,
* measure accuracy, and
* compute the probability over output classes given an input, to base a classification decision on (note that the unnormalized log posterior `z` can often be used for classification directly).

The example network uses category labels, which are represented as one-hot vectors. For the MNIST example these will appear as an array of 10 floating point values, all of which are zero except for the proper label category which is *1.0*.
Classification tasks like ours commonly use the `SoftMax()` function to obtain the probabilities for each label. The network is then optimized to maximize the log probability of the correct class (cross-entropy), and minimize that of all other classes. This is our *training criterion*, or *loss function*. In CNTK these two actions are typically combined in one function for efficiency:

    ce = CrossEntropyWithSoftmax (labels, z)

`CrossEntropyWithSoftmax()` function takes the input, computes the `SoftMax()` function, calculates the error from the actual value using cross-entropy, and that error signal is used to update the parameters in the network via back propagation. Hence, in the example above, the normalized `Softmax()` value, which we computed as `P`, is not used during training. It will, however, be needed for *using* the network (again note that in many cases, `z` is often sufficient for classification; in that case, `z` itself would be the output).

CNTK uses *Stochastic Gradient Descent (SGD)* as the learning algorithm. SGD needs to compute the gradient of the objective function with respect to all model parameters. Importantly, CNTK does not require users to specify those gradients. Rather, each built-in function in CNTK also has a derivative counterpart function, and the system automatically performs the back propagation update of the network parameters. This is not visible to the user. Users never have to be concerned with gradients. Ever.

In addition to the training criterion, predicted error rates are often computed during the training phase to validate improvement of the system as the training is going further. This is handled in CNTK using the following function:

    errs = ClassificationError (labels, z)

The probabilities produced by the network are compared to the actual label and the error rate is computed. This is generally displayed by the system. Though this is useful, it is not mandatory to use `ClassificationError()`.

#### Communicating Inputs, Outputs, and Criteria to the System
Now that all variables are defined, we must tell the system which of the variables it should treat as inputs, outputs, and criteria. This is done by defining 5 special variables which must have exactly these names:

    featureNodes    = (features)
    labelNodes      = (labels)
    criterionNodes  = (ce)
    evaluationNodes = (errs)
    outputNodes     = (z:P)

The values are arrays, where the values should be separated by a colon (the colon `:` is a BrainScript operator that forms an array by concatenating two values or arrays). This is shown above for `outputNodes`, which declares both `z` and `P` as outputs.

(Note: The deprecated `NDLNetworkBuilder` required the array elements to be separated by commas instead.)

## Summary of Special Names
As we saw above, there are 7 special names we must be aware of, which carry "magic" properties:

* `ParameterTensor{}`: Declares and initializes a learnable parameter.
* `Input{}`: Declares a variable that gets connected and fed from a data reader.
* `featureNodes`, `labelNodes`, `criterionNodes`, `evaluationNodes`, and `outputNodes`: Declares to the system which of our variables to use as inputs, outputs, and criteria.

In addition, there are 3 more special functions with built-in "magic" in CNTK, which are discussed elsewhere:

* `Constant()`: Declares a constant.
* `PastValue()` and `FutureValue()`: Access a variable in a network function at a different time step, for form recurrent loops.

Any other predefined name is either a built-in primitive function such as `Sigmoid()` or `Convolution()` with a C++ implementation, a predefined library function realized in BrainScript such as `BS.RNNs.LSTMP()`, or a record that acts as a namespace for library functions (e.g. `BS.RNNs`). See [BrainScript-Full-Function-Reference](./BrainScript-Full-Function-Reference.md) for a full list.

Next: [BrainScript Expressions](./BrainScript-Expressions.md)
