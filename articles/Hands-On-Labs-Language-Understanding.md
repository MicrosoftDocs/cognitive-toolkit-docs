---
title:   Hands-on Labs Language Understanding
author:    chrisbasoglu
date:    11/14/2016
ms.author:   cbasoglu
ms.date:   11/14/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# Hands-on Labs Language Understanding with Recurrent Networks

Note that this tutorial requires the latest master version, or the upcoming CNTK 1.7.1 which will be released soon.

This hands-on lab shows how to implement a recurrent network to process text,
for the Air Travel Information Services (ATIS) tasks of slot tagging and intent classification.
We will start with a straight-forward embedding followed by a recurrent LSTM.
We will then extend it to include neighbor words and run bidirectionally.
Lastly, we will turn this system into an intent classifier.  

The techniques you will practice include:

* model description by composing layer blocks instead of writing formulas
* creating your own layer block
* variables with different sequence lengths in the same network
* parallel training

We assume that you are familiar with basics of deep learning, and these specific concepts:

* recurrent networks ([Wikipedia page](https://en.wikipedia.org/wiki/Recurrent_neural_network))
* text embedding ([Wikipedia page](https://en.wikipedia.org/wiki/Word_embedding))

## Prerequisites

We assume that you have already installed CNTK and can run the CNTK command.
This tutorial was held at KDD 2016 and requires a recent build,
please [see here](./Setup-CNTK-on-your-machine) for setup instructions. You can just follow the instructions 
for downloading a binary install package from that page. 

Next, please download a ZIP archive (about 12 MB): Click on [this link](https://github.com/Microsoft/CNTK/blob/fseide/kdd/Tutorials/CNTK_HandsOn_KDD2016.zip),
and then on the Download button.
The archive contains the files for this tutorial. Please the archive
and set your working directory to `SLUHandsOn`.
The files you will be working with are:

* [`SLUHandsOn.cntk`](https://github.com/Microsoft/CNTK/blob/Tutorials/SLUHandsOn/SLUHandsOn.cntk): The CNTK configuration file we
  will introduce below and work with.
* [`slu.forward.nobn.cmf`](https://github.com/Microsoft/CNTK/blob/master/Tutorials/SLUHandsOn/slu.forward.nobn.cmf),
  [`slu.forward.cmf`](https://github.com/Microsoft/CNTK/blob/master/Tutorials/SLUHandsOn/slu.forward.cmf),
  [`slu.forward.lookahead.cmf`](https://github.com/Microsoft/CNTK/blob/master/Tutorials/SLUHandsOn/slu.forward.lookahead.cmf), and
  [`slu.forward.backward.cmf`](https://github.com/Microsoft/CNTK/blob/master/Tutorials/SLUHandsOn/slu.forward.backward.cmf):
  Pre-trained models that are the result of the respective configurations we are developing throughout this tutorial.
* `atis.train.ctf` and `atis.test.ctf`: The training and test corpus, already converted to CNTK Text Format (CTF).

Lastly, we strongly recommend to run this on a machine with a capable CUDA-compatible GPU.
Deep learning without GPUs is not fun.

## Task and Model Structure

The task we want to approach in this tutorial is slot tagging.
We use the [ATIS corpus](https://catalog.ldc.upenn.edu/LDC95S26).
ATIS contains human-computer queries from the domain of Air Travel Information Services,
and our task will be to annotate (tag) each word of a query whether it belongs to a
specific item of information (slot), and which one.

The data in your working folder has already been converted into the "CNTK Text Format."
Let's look at an example from the test-set file `atis.test.ctf`:

    19  |S0 178:1 |# BOS      |S1 14:1 |# flight  |S2 128:1 |# O
    19  |S0 770:1 |# show                         |S2 128:1 |# O
    19  |S0 429:1 |# flights                      |S2 128:1 |# O
    19  |S0 444:1 |# from                         |S2 128:1 |# O
    19  |S0 272:1 |# burbank                      |S2 48:1  |# B-fromloc.city_name
    19  |S0 851:1 |# to                           |S2 128:1 |# O
    19  |S0 789:1 |# st.                          |S2 78:1  |# B-toloc.city_name
    19  |S0 564:1 |# louis                        |S2 125:1 |# I-toloc.city_name
    19  |S0 654:1 |# on                           |S2 128:1 |# O
    19  |S0 601:1 |# monday                       |S2 26:1  |# B-depart_date.day_name
    19  |S0 179:1 |# EOS                          |S2 128:1 |# O

This file has 7 columns:

* a sequence id (19). There are 11 entries with this sequence id. This means that sequence 19 consists
of 11 tokens;
* column `S0`, which contains numeric word indices;
* a comment column denoted by `#`, to allow a human reader to know what the numeric word index stands for;
Comment columns are ignored by the system. `BOS` and `EOS` are special words
to denote beginning and end of sentence, respectively;
* column `S1` is an intent label, which we will only use in the last part of the tutorial;
* another comment column that shows the human-readable label of the numeric intent index;
* column `S2` is the slot label, represented as a numeric index; and
* another comment column that shows the human-readable label of the numeric label index.

The task of the neural network is to look at the query (column `S0`) and predict the
slot label (column `S2`).
As you can see, each word in the input gets assigned either an empty label `O`
or a slot label that begins with `B-` for the first word, and with `I-` for any
additional consecutive word that belongs to the same slot.

The model we will use is a recurrent model consisting of an embedding layer,
a recurrent LSTM cell, and a dense layer to compute the posterior probabilities:


    slot label   "O"        "O"        "O"        "O"  "B-fromloc.city_name"
                  ^          ^          ^          ^          ^
                  |          |          |          |          |
              +-------+  +-------+  +-------+  +-------+  +-------+
              | Dense |  | Dense |  | Dense |  | Dense |  | Dense |  ...
              +-------+  +-------+  +-------+  +-------+  +-------+
                  ^          ^          ^          ^          ^
                  |          |          |          |          |
              +------+   +------+   +------+   +------+   +------+   
         0 -->| LSTM |-->| LSTM |-->| LSTM |-->| LSTM |-->| LSTM |-->...
              +------+   +------+   +------+   +------+   +------+   
                  ^          ^          ^          ^          ^
                  |          |          |          |          |
              +-------+  +-------+  +-------+  +-------+  +-------+
              | Embed |  | Embed |  | Embed |  | Embed |  | Embed |  ...
              +-------+  +-------+  +-------+  +-------+  +-------+
                  ^          ^          ^          ^          ^
                  |          |          |          |          |
    w      ------>+--------->+--------->+--------->+--------->+------... 
                 BOS      "show"    "flights"    "from"   "burbank"

Or, as a CNTK network description. Please have a quick look and match it with the description above:

        model = Sequential (
            EmbeddingLayer {150} :
            RecurrentLSTMLayer {300} :
            DenseLayer {labelDim}
        )

Descriptions of these functions can be found at: [`Sequential()`](./Sequential.md), [`EmbeddingLayer{}`](./BrainScript-Layers-Reference.md#embeddinglayer), [`RecurrentLSTMLayer{}`](./BrainScript-Layers-Reference.md#recurrentlstmlayer-recurrentlstmlayerstack),
and [`DenseLayer{}`](./BrainScript-Layers-Reference.md#denselayer-linearlayer)

## CNTK Configuration

### Config File

To train and test a model in CNTK, we need to provide a configuration file that tells CNTK what operations you want to run (`command` variable), and a parameter section for every command.

For the training command, CNTK needs to be told:

* how to read the data (`reader` section) 
* the model function and its inputs and outputs in the computation graph (`BrainScriptNetworkBuilder` section)
* hyper-parameters for the learner (`SGD` section)

For the evaluation command, CNTK needs to know how to read the test data (`reader` section).

The following is the configuration file we will start with. As you see, a CNTK configuration file is a text file consisting of definitions of parameters, which are organized in a hierarchy of records. You can also see how CNTK supports basic parameter replacement using the `$parameterName$` syntax. The actual file contains just a few more parameters than mentioned above, but please scan it and locate the configuration items just mentioned:

```
# CNTK Configuration File for creating a slot tagger and an intent tagger.

command = TrainTagger:TestTagger

makeMode = false ; traceLevel = 0 ; deviceId = "auto"

rootDir = "." ; dataDir  = "$rootDir$" ; modelDir = "$rootDir$/Models"

modelPath = "$modelDir$/slu.cmf"

vocabSize = 943 ; numLabels = 129 ; numIntents = 26    # number of words in vocab, slot labels, and intent labels

# The command to train the LSTM model
TrainTagger = {
    action = "train"
    BrainScriptNetworkBuilder = {
        inputDim = $vocabSize$
        labelDim = $numLabels$
        embDim = 150
        hiddenDim = 300

        model = Sequential (
            EmbeddingLayer {embDim} :                            # embedding
            RecurrentLSTMLayer {hiddenDim, goBackwards=false} :  # LSTM
            DenseLayer {labelDim}                                # output layer
        )

        # features
        query      = Input {inputDim}
        slotLabels = Input {labelDim}

        # model application
        z = model (query)

        # loss and metric
        ce   = CrossEntropyWithSoftmax (slotLabels, z)
        errs = ClassificationError     (slotLabels, z)

        featureNodes    = (query)
        labelNodes      = (slotLabels)
        criterionNodes  = (ce)
        evaluationNodes = (errs)
        outputNodes     = (z)
    }

    SGD = {
        maxEpochs = 8 ; epochSize = 36000

        minibatchSize = 70

        learningRatesPerSample = 0.003*2:0.0015*12:0.0003
        gradUpdateType = "fsAdaGrad"
        gradientClippingWithTruncation = true ; clippingThresholdPerSample = 15.0

        firstMBsToShowResult = 10 ; numMBsToShowResult = 100
    }

    reader = {
        readerType = "CNTKTextFormatReader"
        file = "$DataDir$/atis.train.ctf"
        randomize = true
        input = {
            query        = { alias = "S0" ; dim = $vocabSize$ ;  format = "sparse" }
            intentLabels = { alias = "S1" ; dim = $numIntents$ ; format = "sparse" }
            slotLabels   = { alias = "S2" ; dim = $numLabels$ ;  format = "sparse" }
        }
    }
}

# Test the model's accuracy (as an error count)
TestTagger = {
    action = "eval"
    modelPath = $modelPath$
    reader = {
        readerType = "CNTKTextFormatReader"
        file = "$DataDir$/atis.test.ctf"
        randomize = false
        input = {
            query        = { alias = "S0" ; dim = $vocabSize$ ;  format = "sparse" }
            intentLabels = { alias = "S1" ; dim = $numIntents$ ; format = "sparse" }
            slotLabels   = { alias = "S2" ; dim = $numLabels$ ;  format = "sparse" }
        }
    }
}
```

### A Brief Look at Data and Data Reading

We already looked at the data.
But how do you generate this format?
For reading text, this tutorial uses the `CNTKTextFormatReader`. It expects the input data to be
of a specific format, which is described [here](./CNTKTextFormat-Reader.md).

For this tutorial, we created the corpora by two steps:
* convert the raw data into a plain text file that contains of TAB-separated columns of space-separated text. For example:

  ```
  BOS show flights from burbank to st. louis on monday EOS (TAB) flight (TAB) O O O O B-fromloc.city_name O B-toloc.city_name I-toloc.city_name O B-depart_date.day_name O
  ````

  This is meant to be compatible with the output of the `paste` command.
* convert it to CNTK Text Format (CTF) with the following command:

  ```
  python Scripts/txt2ctf.py --map query.wl intent.wl slots.wl --annotated True --input atis.test.txt --output atis.test.ctf
  ```

  where the three `.wl` files give the vocabulary as plain text files, one line per word.

In these CTFG files, our columns are labeled `S0`, `S1`, and `S2`.
These are connected to the actual network inputs by the corresponding lines in the reader definition:

    input = {
        query        = { alias = "S0" ; dim = $vocabSize$ ;  format = "sparse" }
        intentLabels = { alias = "S1" ; dim = $numIntents$ ; format = "sparse" }
        slotLabels   = { alias = "S2" ; dim = $numLabels$ ;  format = "sparse" }
    }

### Running it

You can find the above configuration file under the name `SLUHandsOn.cntk` in the working folder.
To run it,
please execute the above configuration by this command:

    cntk  configFile=SLUHandsOn.cntk

This will execute our configuration, beginning with model training
as defined in the section we named `TrainTagger`.
After a somewhat chatty initial log output, you will soon see this:

    Training 721479 parameters in 6 parameter tensors.

followed by output like this:

    Finished Epoch[ 1 of 8]: [Training] ce = 0.77274927 * 36007; errs = 15.344% * 36007
    Finished Epoch[ 2 of 8]: [Training] ce = 0.27009664 * 36001; errs = 5.883% * 36001
    Finished Epoch[ 3 of 8]: [Training] ce = 0.16390425 * 36005; errs = 3.688% * 36005
    Finished Epoch[ 4 of 8]: [Training] ce = 0.13121604 * 35997; errs = 2.761% * 35997
    Finished Epoch[ 5 of 8]: [Training] ce = 0.09308497 * 36000; errs = 2.028% * 36000
    Finished Epoch[ 6 of 8]: [Training] ce = 0.08537533 * 35999; errs = 1.917% * 35999
    Finished Epoch[ 7 of 8]: [Training] ce = 0.07477648 * 35997; errs = 1.686% * 35997
    Finished Epoch[ 8 of 8]: [Training] ce = 0.06114417 * 36018; errs = 1.380% * 36018

This shows how learning proceeds over epochs (passes through the data).
For example, after two epochs, the cross-entropy criterion, which we had named `ce` in
the configuration file, has reached 0.27 as measured on the 36001 samples of this epoch,
and that the error rate is 5.883% on those same 36016 training samples.

The 36001 comes from the fact that our configuration defined the epoch size as 36000.
The epoch size is the number of samples--counted as *word tokens*, not sentences--to
process between model checkpoints.
Since the sentences have varying length and do not necessarily sum up to
multiples of precisely 36000 words, you will see some small variation.

Once the training has completed (a little less than 2 minutes on a Titan-X or a Surface Book),
CNTK will proceed with the `EvalTagger` action

    Final Results: Minibatch[1-1]: errs = 2.922% * 10984; ce = 0.14306181 * 10984; perplexity = 1.15380111

I.e., on our testing set, slot labels have been predicted with an error rate of 2.9%.
Not at all bad, for such a simple system!

On a CPU-only machine, it can be 4 or more times slower.
To make sure early that the system is progressing,
you can enable tracing to see partial results, which should appear reasonably quickly:

    cntk  configFile=SLUHandsOn.cntk  traceLevel=1

    Epoch[ 1 of 8]-Minibatch[   1-   1, 0.19%]: ce = 4.86535690 * 67; errs = 100.000% * 67 
    Epoch[ 1 of 8]-Minibatch[   2-   2, 0.39%]: ce = 4.83886670 * 63; errs = 57.143% * 63
    Epoch[ 1 of 8]-Minibatch[   3-   3, 0.58%]: ce = 4.78657442 * 68; errs = 36.765% * 68
    ...

If you do not want to wait until this finishes, you can run an intermediate model, e.g.

    cntk  configFile=SLUHandsOn.cntk  command=TestTagger  modelPath=Models/slu.cmf.4
    Final Results: Minibatch[1-1]: errs = 3.851% * 10984; ce = 0.18932937 * 10984; perplexity = 1.20843890

or test our pre-trained model as well, which you can find in the working folder:

    cntk  configFile=SLUHandsOn.cntk  command=TestTagger  modelPath=slu.forward.nobn.cmf
    Final Results: Minibatch[1-1]: errs = 2.922% * 10984; ce = 0.14306181 * 10984; perplexity = 1.15380111

## Modifying the Model

In the following, you will be given tasks to practice modifying CNTK configurations.
The solutions are given at the end of this document... but please try without!

### A Word About [`Sequential()`](./Sequential.md)

Before jumping to the tasks, let's have a look again at the model we just ran.
The model is described in what we call *function-composition style*.

        model = Sequential (
            EmbeddingLayer {embDim} :                            # embedding
            RecurrentLSTMLayer {hiddenDim, goBackwards=false} :  # LSTM
            DenseLayer {labelDim, initValueScale=7}              # output layer
        )

where the colon (`:`) is BrainScript's syntax of expressing arrays. For example,
`(F:G:H)` is an array with three elements, `F`, `G`, and `H`.

You may be familiar with the "sequential" notation from other neural-network toolkits.
If not,
[`Sequential()`](./Sequential.md) is a powerful operation that,
in a nutshell, allows to compactly express a very common situation in neural networks
where an input is processed by propagating it through a progression of layers.
`Sequential()` takes an array of functions as its argument,
and returns a *new* function that invokes these function in order,
each time passing the output of one to the next.
For example,

	FGH = Sequential (F:G:H)
    y = FGH (x)

means the same as

    y = H(G(F(x))) 

This is known as ["function composition"](https://en.wikipedia.org/wiki/Function_composition),
and is especially convenient for expressing neural networks, which often have this form:

         +-------+   +-------+   +-------+
    x -->|   F   |-->|   G   |-->|   H   |--> y
         +-------+   +-------+   +-------+

Coming back to our model at hand, the `Sequential` expression simply
says that our model has this form:

         +-----------+   +----------------+   +------------+
    x -->| Embedding |-->| Recurrent LSTM |-->| DenseLayer |--> y
         +-----------+   +----------------+   +------------+
 
### Task 1: Add Batch Normalization

We now want to add new layers to the model, specifically batch normalization.

Batch normalization is a popular technique for speeding up convergence.
It is often used for image-processing setups, for example our other [hands-on lab on image
recognition](./Hands-On-Labs-Image-Recognition.md).
But could it work for recurrent models, too?
  
So your task will be to insert batch-normalization layers before and after the recurrent LSTM layer.
If you have completed the [hands-on labs on image processing](./Hands-On-Labs-Image-Recognition.md),
you may remember that the [batch-normalization layer](./BrainScript-Layers-Reference.md#batchnormalizationlayer-layernormalizationlayer-stabilizerlayer) has this form:

    BatchNormalizationLayer{}

So please go ahead and modify the configuration and see what happens.

If everything went right, you will notice not only improved convergence speed (`ce` and `errs`)
compared to the previous configuration,
but also a better error rate of 2.0% (compared to 2.9%):

    Training 722379 parameters in 10 parameter tensors.

    Finished Epoch[ 1 of 8]: [Training] ce = 0.29396894 * 36007; errs = 5.621% * 36007 
    Finished Epoch[ 2 of 8]: [Training] ce = 0.10104186 * 36001; errs = 2.280% * 36001
    Finished Epoch[ 3 of 8]: [Training] ce = 0.05012737 * 36005; errs = 1.258% * 36005
    Finished Epoch[ 4 of 8]: [Training] ce = 0.04116407 * 35997; errs = 1.108% * 35997
    Finished Epoch[ 5 of 8]: [Training] ce = 0.02602344 * 36000; errs = 0.756% * 36000
    Finished Epoch[ 6 of 8]: [Training] ce = 0.02234042 * 35999; errs = 0.622% * 35999
    Finished Epoch[ 7 of 8]: [Training] ce = 0.01931362 * 35997; errs = 0.667% * 35997
    Finished Epoch[ 8 of 8]: [Training] ce = 0.01714253 * 36018; errs = 0.522% * 36018

    Final Results: Minibatch[1-1]: errs = 2.039% * 10984; ce = 0.12888706 * 10984; perplexity = 1.13756164

(If you don't want to wait for the training to complete, you can find
the resulting model under the name `slu.forward.cmf`.)

Please see the solution [here](#solution-1-adding-batch-normalization).

### Task 2: Add a Lookahead 

Our recurrent model suffers from a structural deficit:
Since the recurrence runs from left to right, the decision for a slot label
has no information about upcoming words. The model is a bit lopsided.
Your task will be to modify the model such that
the input to the recurrence consists not only of the current word, but also of the next one
(lookahead).

Your solution should be in function-composition style.
Hence, you will need to write a BrainScript function that does the following:

* accept one input argument;
* compute the immediate "future value" of this input using the `FutureValue()` function (use this specific form: `FutureValue (0, input, defaultHiddenActivation=0)`); and
* concatenate the two into a vector of twice the embedding dimension using `Splice()` (use this form: Splice `(x:y)`)

and then insert this function into the `Sequence()` between the embedding and the recurrent layer.
If all goes well, you will see the following output:

    Training 902679 parameters in 10 parameter tensors.

    Finished Epoch[ 1 of 8]: [Training] ce = 0.30500536 * 36007; errs = 5.904% * 36007
    Finished Epoch[ 2 of 8]: [Training] ce = 0.09723847 * 36001; errs = 2.167% * 36001
    Finished Epoch[ 3 of 8]: [Training] ce = 0.04082365 * 36005; errs = 1.047% * 36005
    Finished Epoch[ 4 of 8]: [Training] ce = 0.03219930 * 35997; errs = 0.867% * 35997
    Finished Epoch[ 5 of 8]: [Training] ce = 0.01524993 * 36000; errs = 0.414% * 36000
    Finished Epoch[ 6 of 8]: [Training] ce = 0.01367533 * 35999; errs = 0.383% * 35999
    Finished Epoch[ 7 of 8]: [Training] ce = 0.00937027 * 35997; errs = 0.278% * 35997
    Finished Epoch[ 8 of 8]: [Training] ce = 0.00584430 * 36018; errs = 0.147% * 36018

    Final Results: Minibatch[1-1]: errs = 1.839% * 10984; ce = 0.12023170 * 10984; perplexity = 1.12775812

This worked! Knowing what the next word is allows the slot tagger to reduce its
error rate from 2.0% to 1.84%.

(If you don't want to wait for the training to complete, you can find
the resulting model under the name `slu.forward.lookahead.cmf`.)

Please see the solution [here](#solution-2-add-a-lookahead).

### Task 3: Bidirectional Recurrent Model

Aha, knowledge of future words help. So instead of a one-word lookahead,
why not look ahead until all the way to the end of the sentence, through a backward recurrence?
Let us create a bidirectional model!

Your task is to implement a new layer that
performs both a forward and a backward recursion over the data, and
concatenates the output vectors.

Note, however, that this differs from the previous task in that
the bidirectional layer contains learnable model parameters.
In function-composition style,
the pattern to implement a layer with model parameters is to write a *factory function*
that creates a *function object*.

A function object, also known as *functor*, is an object that is both a function and an object.
Which means nothing else that it contains data yet still can be invoked as if it was a function.

For example, `LinearLayer{outDim}` is a factory function that returns a function object that contains
a weight matrix `W`, a bias `b`, and another function to compute `W * input + b`.
E.g. saying `LinearLayer{1024}` will create this function object, which can then be used
like any other function, also immediately: `LinearLayer{1024}(x)`. 

Confused? Let's take an example: Let us implement a new layer that combines
a linear layer with a subsequent batch normalization. 
To allow function composition, the layer needs to be realized as a factory function,
which could look like this:

    LinearLayerWithBN {outDim} = {
        F = LinearLayer {outDim}
        G = BatchNormalization {normalizationTimeConstant=2048}
        apply (x) = G(F(x))
    }.apply

Invoking this factory function will first create a record (indicated by `{...}`) with three members: `F`, `G`,
and `apply`. In this example, `F` and `G` are function objects themselves, and `apply` is the function
to be applied to the data.
Appending `.apply` to this expression means what `.x` always means in BrainScript, to
access a record member. Thus, e.g. calling `LinearLayerWithBN{1024}` will
create an object containing a linear-layer function object called `F`, a batch-normalization function object `G`,
and `apply` which is the function that implements the actual operation of this layer
using `F` and `G`. It will then return `apply`. To the outside, `apply()` looks and behaves
like a function. Under the hood, however, `apply()` will hold on to the record it belongs to,
and thus retain access to its specific instances of `F` and `G`.

Now back to our task at hand. You will now need to create a factory function,
very much like the example above.
You shall create a factory function
that creates two recurrent layer instances (one forward, one backward), and then defines an `apply (x)` function
which applies both layer instances to the same `x` and concatenate the two results.

Allright, give it a try! To know how to realize a backward recursion in CNTK,
please take a hint from how the forward recursion is done.
Please also do the following:
* remove the one-word lookahead you added in the previous
task, which we aim to replace; and
* change the `hiddenDim` parameter from 300 to 150, to keep the total number of model parameters
limited.

Running this model successfully will produce the following output:

    Training 542379 parameters in 13 parameter tensors.

    Finished Epoch[ 1 of 8]: [Training] ce = 0.27651655 * 36007; errs = 5.288% * 36007
    Finished Epoch[ 2 of 8]: [Training] ce = 0.08179804 * 36001; errs = 1.869% * 36001
    Finished Epoch[ 3 of 8]: [Training] ce = 0.03528780 * 36005; errs = 0.828% * 36005
    Finished Epoch[ 4 of 8]: [Training] ce = 0.02602517 * 35997; errs = 0.675% * 35997
    Finished Epoch[ 5 of 8]: [Training] ce = 0.01310307 * 36000; errs = 0.386% * 36000
    Finished Epoch[ 6 of 8]: [Training] ce = 0.01310714 * 35999; errs = 0.358% * 35999
    Finished Epoch[ 7 of 8]: [Training] ce = 0.00900459 * 35997; errs = 0.300% * 35997
    Finished Epoch[ 8 of 8]: [Training] ce = 0.00589050 * 36018; errs = 0.161% * 36018

    Final Results: Minibatch[1-1]: errs = 1.830% * 10984; ce = 0.11924878 * 10984; perplexity = 1.12665017

Works like a charm! This model achieves 1.83%, a tiny bit better than the lookahead model above.
The bidirectional model has 40% less parameters than the lookahead one. However, if you go back and look closely
at the complete log output (not shown on this web page), you may find that the lookahead one trained
about 30% faster.
This is because the lookahead model has both less horizontal dependencies (one instead of two
recurrences) and larger matrix products, and can thus achieve higher parallelism.

Please see the solution [here](#solution-3-bidirectional-recurrent-model).

### Task 4: Intent Classification

It turns out that the model we constructed so far can easily be turned into
an intent classifier.
Remember that our data file contained this additional column called `S1`.
This column contains a single label per sentence, indicating the query's intent
of finding information about topics such as `airport` or `airfare`.

The task of classifying an entire sequence into a single label is called
*sequence classification*.
Our sequence classifier will be implemented as a recurrent LSTM (we already have that)
of which we take its hidden state of its *final step*.
This gives us a single vector for each sequence.
This vector is then fed into a dense layer for softmax classification.

CNTK has an operation to extract the last state from a sequence, called `BS.Sequences.Last()`.
This operation honors the fact that the same minibatch may contain sequences
of very different lengths, and that they are arranged in memory in a packed format.
Likewise, for backward recursion, we can use `BS.Sequences.First()`.

Your task is to modify the bidirectional network from Task 3 such that the last frame is extracted
from the forward recursion, and the first frame is extracted from the backward recursion,
and the two vectors are concatenated. The concatenated vector (sometimes called a *thought vector*)
shall then be the input of the dense layer.

Also, you must change the label from the slot to the intent label:
Just rename the input variable (`slotLabels`) to instead match
the name that is used in the reader section for the intent labels,
and also match the dimension.

Please try the modification. If you do it right, you will, however, be confronted
with a vexing error message, and a long one at that:

    EXCEPTION occurred: Dynamic axis layout '*' is shared between inputs 'intentLabels'
    and 'query', but layouts generated from the input data are incompatible on this axis.
    Are you using different sequence lengths? Did you consider adding a DynamicAxis()
    to the Input nodes?

"Are you using different sequence lengths?" Oh yes! The query and the intent label do--the intent label
is only one single token per query. It is a sequence of 1 element! So how to fix this?

CNTK allows different variables in the network to have different sequence lengths.
You can think of the sequence length as an additional, symbolic tensor dimension.
Variables of the same length share the same symbolic length dimension.
If two variables have different lengths, this must be explicitly declared,
otherwise CNTK will assume that all variables share the same symbolic length.

This is done by creating a new *dynamic axis* object and associating it with one of the inputs, as follows:

        n = DynamicAxis()
        query = Input {inputDim, dynamicAxis=n}

CNTK has a default axis. As you might guess from the exception above, its name is '*'.  
Hence, you only need to declare one new axis;
the other Input (`intentLabels`) will continue to use the default axis.

Now we should be good to run, and see the following output:

    Training 511376 parameters in 13 parameter tensors.

    Finished Epoch[ 1 of 8]: [Training] ce = 1.17365003 * 2702; errs = 21.318% * 2702
    Finished Epoch[ 2 of 8]: [Training] ce = 0.40112341 * 2677; errs = 9.189% * 2677
    Finished Epoch[ 3 of 8]: [Training] ce = 0.17041608 * 2688; errs = 4.167% * 2688
    Finished Epoch[ 4 of 8]: [Training] ce = 0.09521124 * 2702; errs = 2.739% * 2702
    Finished Epoch[ 5 of 8]: [Training] ce = 0.08287138 * 2697; errs = 2.262% * 2697
    Finished Epoch[ 6 of 8]: [Training] ce = 0.07138554 * 2707; errs = 2.032% * 2707
    Finished Epoch[ 7 of 8]: [Training] ce = 0.06220047 * 2677; errs = 1.419% * 2677
    Finished Epoch[ 8 of 8]: [Training] ce = 0.05072431 * 2686; errs = 1.340% * 2686

    Final Results: Minibatch[1-1]: errs = 4.143% * 893; ce = 0.27832144 * 893; perplexity = 1.32091072

Without much effort, we have achieved an error rate of 4.1%. Very nice for a first shot
(although not quite state of the art on this task, which is at 3%).

You may notice one thing though: The number of samples per epoch is now around 2700.
This is because this is the number of label samples, of which we now only have one
per sentence. We have a greatly reduced number of supervision signals in this task.
This should encourage us to try to increase the minibatch size.
Let's try 256 instead of 70:

    Finished Epoch[ 1 of 8]: [Training] ce = 1.11500325 * 2702; errs = 19.282% * 2702
    Finished Epoch[ 2 of 8]: [Training] ce = 0.29961089 * 2677; errs = 6.052% * 2677
    Finished Epoch[ 3 of 8]: [Training] ce = 0.09018802 * 2688; errs = 2.418% * 2688
    Finished Epoch[ 4 of 8]: [Training] ce = 0.04838102 * 2702; errs = 1.258% * 2702
    Finished Epoch[ 5 of 8]: [Training] ce = 0.02996789 * 2697; errs = 0.704% * 2697
    Finished Epoch[ 6 of 8]: [Training] ce = 0.02142932 * 2707; errs = 0.517% * 2707
    Finished Epoch[ 7 of 8]: [Training] ce = 0.01220149 * 2677; errs = 0.299% * 2677
    Finished Epoch[ 8 of 8]: [Training] ce = 0.01312233 * 2686; errs = 0.186% * 2686

This system learns much better! (Note, though, that this difference is
likely an artifact caused by our `fsAdagrad` gradient-normalization scheme,
and typically will disappear soon when using larger data sets.)

The resulting error rate is higher, though:

    Final Results: Minibatch[1-1]: errs = 4.479% * 893; ce = 0.31638223 * 893; perplexity = 1.37215463

but this difference actually corresponds to 3 errors, which is not significant.

Please see the solution [here](#solution-4-intent-classification).

### Task 5: Parallel Training

Lastly, if you have multiple GPUs, CNTK allows you to parallelize the training using
MPI (Message-Passing Interface).
This model is too small to expect any speed-up; parallelizing such a small model
will severely underutilize available GPUs.
Nevertheless, let us go through the motions, so that you know how to do it
once you move on to real-world workloads.

Please add the following lines to the `SGD` block:

    SGD = {
        ...
        parallelTrain = {
            parallelizationMethod = "DataParallelSGD"
            parallelizationStartEpoch = 1
            distributedMBReading = true
            dataParallelSGD = { gradientBits = 2 }
        }
    }

and then execute this command:

    mpiexec -np 4 cntk  configFile=SLUHandsOn_Solution4.cntk  stderr=Models/log  parallelTrain=true  command=TrainTagger

This will run the training on 4 GPUs using the 1-bit SGD algorithm (2-bit SGD in this case, actually).
Its approximation did not hurt accuracy: 
The error rate is 4.367%, two errors more (please run the `TestTagger` action separately on a single GPU).

# Conclusion

This tutorial has introduced function-composition style as a compact means of representing networks.
Many neural-network types are suitable for representing them this way,
which is a more direct and less error-prone translation of a graph into a network description.

This tutorial practiced to take an existing configuration in function-composition style,
and modifying it in specific ways:

* adding a layer (from our gallery of predefined layers)
* defining and using a function 
* defining and using a layer factory function

The tutorial also discussed handling of multiple time dimensions,
and we have seen how to parallelize the training.

# Solutions

Below are the solutions to the tasks above. Hey, no cheating!

### Solution 1: Adding Batch Normalization

The modified model function has this form:

        model = Sequential (
            EmbeddingLayer {embDim} :                            # embedding
            BatchNormalizationLayer {} :           ##### added
            RecurrentLSTMLayer {hiddenDim, goBackwards=false} :  # LSTM
            BatchNormalizationLayer {} :           ##### added
            DenseLayer {labelDim}                                # output layer
        )

### Solution 2: Add a Lookahead

Your lookahead-function could be defined like this:

        OneWordLookahead (x) = Splice (x : DelayLayer {T=-1} (x))

and it would be inserted into the model like this:

        model = Sequential (
            EmbeddingLayer {embDim} :
            OneWordLookahead :                   ##### added
            BatchNormalizationLayer {} :
            RecurrentLSTMLayer {hiddenDim, goBackwards=false} :
            BatchNormalizationLayer {} :
            DenseLayer {labelDim}
        )

### Solution 3: Bidirectional Recurrent Model

The bidirectional recurrent layer could be written like this:

        BiRecurrentLSTMLayer {outDim} = {
            F = RecurrentLSTMLayer {outDim, goBackwards=false}
            G = RecurrentLSTMLayer {outDim, goBackwards=true}
            apply (x) = Splice (F(x):G(x))
        }.apply

and then used like this:

        hiddenDim = 150      ##### changed from 300 to 150

        model = Sequential (
            EmbeddingLayer {embDim} :
            ###OneWordLookahead :                   ##### removed
            BatchNormalizationLayer {} :
            BiRecurrentLSTMLayer {hiddenDim} :
            BatchNormalizationLayer {} :
            DenseLayer {labelDim}
        )

### Solution 4: Intent Classification

Reduce the sequences to the last/first hidden of the recurrent layer: 

            apply (x) = Splice (BS.Sequences.Last(F(x)):BS.Sequences.First(G(x)))
            ##### added Last() and First() calls ^^^


Change the label input from slot to intent:

        intentDim = $numIntents$    ###### name change
        ...
            DenseLayer {intentDim}                      ##### different dimension
        ...
        intentLabels = Input {intentDim}
        ...
        ce   = CrossEntropyWithSoftmax (intentLabels, z)
        errs = ErrorPrediction         (intentLabels, z)
        ...
        labelNodes      = (intentLabels)

Use a new dynamic axis:

        n = DynamicAxis()                               ##### added
        query        = Input {inputDim, dynamicAxis=n}  ##### use dynamic axis

# Acknowledgment

We would like to thank Derek Liu for preparing the basis of this tutorial.
