The main top-level command `train` and `eval`/`test` are described on the [Train, Test, Eval](./Train,-Test,-Eval.md) page. Further top-level commands are detailed below.

## Adapt Command
This command adapts an already trained model using KL divergence regularization. It is advised that all other adaptations should be carried out using model editing. The adapt command is very similar to the train command except that it carries two more parameters:
* `originalModelFileName`: the file name of the model that will be adapted.

* `refNodeName`: the name of the node in the computational network which will be used for KL divergence regularization.

## CV
This command evaluates a series of models from different epochs on a development (or cross validation) set and displays the information of the best model. Besides the parameters used for the test command, this command also use the parameters
* `crossValidationInterval`: the array of 3 integers identifying the starting epoch, epoch increment and final epoch to evaluate. For example, 3:2:9 means the models 3,5,7, and 9 will be evaluated.

* `sleepTimeBetweenRuns`: how many seconds to wait between runs. This is needed only if your GPU is too hot.

## Write Command
This command writes the value of an output node to a file. The related parameters are
* `reader`: the reader configuration block to read the input data.

* `writer`: the writer configuration block to determine how to write the output data. If this value is not specified the outputPath parameter will be used.

* `minibatchSize`: the minibatch size to use when reading and processing the dataset.

* `epochSize`: the size of dataset. Default is 0. The entire dataset will be evaluated if it’s set to 0.

* `modelPath`: the path to the model to be used to compute the output.

* `outputPath`: the path to the file to write the output in a text format. If the writer block exists this parameter will be ignored. Either outputPath or writer must exist.

* `outputNodeNames`: an array of one or more output node names to be written to a file.

## Edit Command
This command edits a model and saves the modified model to file. This is deprecated. The associated parameters are:

* `editPath`: the path to the MEL script to be executed.
* `ndlMacros`: the path to the NDL macros file that will be loaded and used in the MEL script.

## SVD Command
This command conducts low-rank approximation with SVD decomposition of learnable parameters. The primary motivation is to factorize a weight matrix with two lower-rank matrices to improve inference speed, while preserving accuracy. For example:

    svd = [
        action = "SVD"
        modelPath = "train\lstm.model.67"
        outputModelPath = "train\lstm.model.svd"
        SVDConfig = "nodeConfigs"
    ]

The associated parameter blocks are:
* `modelPath`: specifies where to load the initial model.

* `outputModelPath`: specifies where to save the revised model.

* `SVDConfig`: a config file which specifies how the SVD is performed for different groups of nodes. This config is in a two-column format: 

    `<NodeNameRegex> <float>`

The first column is a regular expression which will match the node name in one group. The second column is a float indicating what percentage of SVDenergy will be kept after SVD, where SVD-energy is defined as the sum of singular values. For example, the config

    `LSTMoutput[1-3].Wx[ifco] 0.4`
    `LSTMoutput[1-3].Wh[ifco] 0.6`

will cause a more aggressive SVD (0.4) decomposition for the non-recurrent connections (from x to i,f,c,o gates) in LSTM and less aggressive SVD decomposition for recurrent connections (from h to i,f,c,o gates), where the parameter names are defined in the NDL.

## Dumpnode Command
This command dumps the information of node(s) to an output file, which can also be accomplished in MEL with greater control. The related parameters are:
* `modelPath`: the path to the model file containing the nodes to dump.

* `nodeName`: the name of the node to be written to a file. If not specified all nodes will be dumped.

* `nodeNameRegex`: the regular expression for the names of the nodes to be written to a file. If specified, the `nodeName` parameter is ignored. 

* `outputFile`: the path to the output file. If not specified a file name will be automatically generated based on the modelPath.

* `printValues`: determines whether to print the values associated with a node if the node’s values are persisted in the model. Default is true.

* `printMetadata`: determines whether to print the metadata (node name, dimensions, etc) associated with a node. Default is true.

## WriteWordAndClass Command (deprecated)
This command reads the text training data, counts the number of occurrences of each each word in the training set, sorts the words in the descending order of the counts, gives each word a unique id, assign each word to a class, and generates
a four column vocabulary file and a word-to-id mapping file to be used by the (deprecated) LMSequenceReader to train class-based language models. The related parameters are:
* `inputFile`: the path to the text training file.

* `outputVocabFile`: the name of the generated four-column vocabulary file. The first column is the word id, the second column is the count of the word, the third column is the word, and the fourth column is the class id.

* `outputWord2Cls`: the path to the generated word-to-class mapping file.

* `outputCls2Index`: the path to the generated class-to-wordId mapping file.

* `vocabSize`: the desired vocabulary size.

* `nbrClass`: the desired number of classes.

* `cutoff`: the cutoff count. When a word’s count is below or equal to this value the word will be treated as <unk>. Default is 2. Note that this parameter is used only if the desired vocabulary size is larger than the actual number of words exist in the training set.

## CreateLabelMap Command
Often it is easy to manually craft the label mapping file. Sometimes, however, you would prefer to have the label mapping file generated automatically which is the purpose of the CreateLabelMap command. Currently UCIFastReader is the only reader that supports this action. The related parameters are 
* `section`: the parameter block name (usually a train block) which has the reader sub-block that will be used to generate the label mapping file. The generated label mapping file will be saved to the labelMappingFile specified in the reader sub-block of this parameter block.

* `minibatchSize`: the minibatch size to use when creating the label mapping file.

## DoEncoderDecoder Command
Neural networks can be used to form a chain of networks. The first several networks can work as encoders and the following networks can serve as decoders. A special node, PairNetworkNode, is used in each network to serve a socket to be
connected by other networks. The related parameters are 
* `section`: the encoderReader and decoderReader are the readers for encoder and decoder. Similarly for encoderCVReader and decoderCVReader for validation set.

* `encoderNetworkBuilder` and `decoderNetworkBuilder`: These specify the simple network builder to used.

## PBStat Command
Evaluating the mean and variance of evaluating and testing data-set for the batch normalization node is tricky. Although the mean and variance could be calculated via running average or exp while training, we still tend to provide a more stable and robust method to generate the mean and variance of batch normalization -- Post batch normalization statistics. The command need to be called after training. It will generate the mean and variance for each BN layer.
* `modelPath`: the path to the model file containing trained model
* `minibatchSize`: the mini-batch size while evaluating, same with training minibatchSize
* `itersPerNode`: the statistics iterations for each batch normalization node
* `reader`: the reader configuration block to read the test data. For details see [Reader block](./Reader-block.md)
* `enableDistributedMBReading`: distributed reading in parallel training




