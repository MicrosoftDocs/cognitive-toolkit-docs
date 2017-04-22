Here we describe the main top-level commands `train` and `test`/`eval` and their corresponding parameters. For additional top-level commands see the [corresponding wiki page](/en-us/cognitive-toolkit/Top-level-commands.md). 

## Train Command
This command asks CNTK to train a model. An example for a `train` command section is provided on the [Config file overview](/en-us/cognitive-toolkit/Brainscript-Config-file-overview.md) page. The related parameters are:
* `reader` – the reader configuration block used to determine how to load input data. For details see [Reader block](/en-us/cognitive-toolkit/Brainscript-Reader-block.md).

* `SGD` – the SGD training setup. For details see [SGD block](/en-us/cognitive-toolkit/Brainscript-SGD-block.md).

* `BrainScriptNetworkBuilder` – the BrainScript configuration block. For details see [BrainScript Network Builder](/en-us/cognitive-toolkit/BrainScript-Network-Builder.md).

* `SimpleNetworkBuilder` – the simple network builder configuration block. For details see [Simple Network Builder](/en-us/cognitive-toolkit/Simple-Network-Builder.md).

* `cvReader` – (optional) the reader configuration block for cross-validation data.

* `makeMode` – if set to `true` (default) the training will continue from whatever epoch interrupted. If set to `false` the training will restart from scratch.

* `firstMBsToShowResult` – indicates for how many minibatches at the beginning of an epoch to show intermediate results for individually.

* `numMBsToShowResult` – indicates after how many minibatches the intermediate results should be shown.

## Test or Eval Command
These commands evaluate/test a model for accuracy, usually with a test dataset. The related parameters are:
* `reader` – the reader configuration block to read the test data. For details see [Reader block](/en-us/cognitive-toolkit/Brainscript-Reader-block.md).

* `modelPath` – the path to the model to be evaluated.

* `BrainScriptNetworkBuilder` – if this is given, the model is not just read from `modelPath` but instead constructed from this configuration. This is used to modify models on the fly for evaluation.

* `minibatchSize` – the minibatch size to use when reading and processing the dataset.

* `epochSize` – the size of dataset. Default is `0`. The entire dataset will be evaluated if it’s set to `0`.

* `numMBsToShowResult` – indicates after how many minibatches the intermediate results should be shown.

* `evalNodeNames` – an array of one or more node names to evaluate.

* `distributedMBReading` - accepts Boolean value: true or false; default is false. It should be set to true for readers that support distributed minibatch reading. If you are using CNTK Text Format Reader, Image Reader, or [Composite Data Reader](./Understanding-and-Extending-Readers), you should set distributedMBReading=true.

The following example is taken form the [Simple2d example](Examples). In that example the `modelPath` is defined on the top level and picked up automatically by both the `train` and `test` command.

    Simple_Demo_Test = [
        action = "test"

        # Parameter values for the reader
        reader = [
            readerType = "CNTKTextFormatReader"
            file = "$DataDir$/SimpleDataTest_cntk_text.txt"
            randomize = false
            input = [
                features = [
                    dim = 2        # two-dimensional input data
                    format = "dense"
                ]
                labels = [
                    dim = 2        # two-dimensional labels
                    format = "dense"
                ]
            ]
        ]
    ]
