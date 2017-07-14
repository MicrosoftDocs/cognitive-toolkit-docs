---
title:   BrainScript HTKMLF Reader
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---
# BrainScript HTKMLF Reader

> [!WARNING]
> HTKMLFReader is obsolete and is being replaced by HTK MLF and Feature deserializers, cf. [Understanding and Extending Readers](./BrainScript-and-Python---Understanding-and-Extending-Readers.md). Please use these for your networks.

HTKMLFReader is a data reader that reads files typically associated with speech recognition tasks, and specifically with the [Hidden Markov Model Toolkit (HTK)](http://htk.eng.cam.ac.uk/) suite of tools. The reader can take as input two types of files, a list of feature files known in HTK parlance as an ```scp``` file (“script” file), a label file known as ```mlf``` file (“model label file”) and an archive file.

HTKMLFReader relies on HTK defined formats of datasets, so we recommend to get familiar with HTK basics by using, e.g. [HTK Book](http://htk.eng.cam.ac.uk/docs/docs.shtml). 

Because CNTK can be used to build networks with arbitrary topology including networks with multiple inputs and outputs, the HTKMLFReader can process one or multiple ```scp``` and ```MLF``` files. A typical example of usage for the HTKMLFReader is as follows:

```
    reader = [
        readerType = "HTKMLFReader"
        readMethod = "blockRandomize"
        miniBatchMode = "partial"
        randomize = "17280000"
        features = [
            dim = 792
            scpFile = "$ScpDir$\TIMIT.train.scp.fbank.fullpath"
        ]
        labels = [
            mlfFile = "$MlfDir$\TIMIT.train.align_cistate.mlf.cntk"
            labelDim = 183
            labelMappingFile = "$MlfDir$\TIMIT.statelist"
        ]
    ]
```

The HTKMLFReader has the following configuration parameters:

* `readMethod`: the method used to read the features files into memory for processing during network training. Setting `readMethod` to `blockRandomize` will divide the data into large blocks and then randomize the order of the blocks and then the order of the data within the block. The data in each block is read directly from the feature files, and released from RAM when it is no longer used. The `blockRandomize` option requires an archive file, described below. Also, as described below, when used in conjunction with `frameMode = "false"` the `blockRandomize` read method will randomize over utterances, but not frames. This is appropriate for use with recurrent architectures when preserving the sequential nature of the training examples is desired.  An alternative is ```rollingWindow``` , which reads in all feature files and stores them on disk in one large temporary binary file. The data is then randomized by running a large rolling window over the data in this file and randomizing the data within the window. This method should only be used when an archive file is not available, and only works in frame mode. The default method is ```blockRandomize```. 

* `pageFilePath`: specify where the temporary page file of the features should be stored. By default it will use system provided temp file.

* `randomize`: This specifies the randomization-window size. CNTK uses a rolling window of this size over the data to sample from. Only the samples inside this rolling window are loaded from disk and held in RAM only as long as needed. The recommended setting for production-size speech corpora is 48 hours, i.e. specify `17280000`. The `randomize` parameter also understands two special values: `auto` or `none`. `none` disables randomization altogether, useful for evaluation or writing output data. `auto` will read the entire corpus into RAM, which is typically not feasible or desirable for production-size data sets of several thousands of hours of speech.

* `minibatchMode`: `partial` or `full`, this option decides how to handle the last minibatch if there are not enough frames to form a complete minibatch of the requested size. The default is `partial`, which will use the remaining frames in a smaller final minibatch of the training epoch. The `full` option will only process complete minibatches.

The above example has two sources of data being processed by the reader, features, in the form of a list of HTK feature files, and labels, which are in the form of an HTK MLF file. Both features and labels correspond to nodes in the computational network, in this case, the input and output nodes, respectively. Note that `features` and `labels` are the default names used by the SimpleNetworkBuilder but if the network is designed using the Network Description Language (NDL), then any names can be used, as long as they each have a corresponding node in the network.

To specify continuous-valued features, e.g. MFCC's or log mel filterbank coefficients, the following parameters should be included in the a configuration block:

* `scpFile`: a list of files to be processed. The files should be HTK compatible files and can be specified in standard format or “archive” format. The details of using an archive are described below. 

* `dim`: an integer that specifies the full feature vector dimension with the desired context window. For example, if you had 72-dimensional features (24-dimensional filterbank features plus delta and delta-delta coefficients) and the network is designed to process a context window of 11 frames, the specified dimension should be 792. Note that only symmetric context windows are supported.

To specify the corresponding labels, e.g. phoneme or senone labels, a configuration block should be used that specifies the following parameters:

* `mlfFile`: an HTK-style ```mlf``` file that contains the labels for all utterances specified in the ```scp``` file.

* `labelDim`: the total cardinality of the label set.

* `labelMappingFile`: the path to a file which lists all the labels seen in the ```mlf``` file, one per line.

Note that multiple inputs and outputs can be specified using additional reader blocks for either features read from files listed in an ```scp``` file or labels read from an ```mlf``` file. For example, in a multi-task learning scenario, where the network was predicting both speaker identity and senone label, the user would specify an additional block that includes an ```mlf``` file that contains labels corresponding about speaker identity. Similarly, for a network with multiple inputs, e.g. both MFCC and PLP features, an additional feature block would be used. 

For recurrent structures, such as an RNN or an LSTM, there are additional configuration options in the HTKMLFReader

* `frameMode`: `true` or `false`, whether the reader should randomize the data at the frame level or the utterance level. Setting `frameMode` to `true` is the default and is appropriate for training networks without any temporal connections. For networks that are designed to learn sequential information, e.g. RNN, `frameMode` should be set to `false`, which means utterances are randomized but proper sequence is maintained within an utterance. Note that this only works with the `blockRandomize` read method.

* `nbruttsineachrecurrentiter`: specifies the number of utterances to process together for efficient training of networks with recurrent structures. The default is `1`, but any value below 20 to 30 will lead to significant efficiency degradations with GPUs.

* `truncated`: `true` or `false`. This enables truncated BPTT.

It is important to note that there are some small differences between the standard ```scp``` and ```mlf``` files used in HTK and the ones used in CNTK.

Most notably, the ```mlf``` file must contain the actual symbols used for classification. For continuous speech recognition, this typically means labels corresponding to the senones (physicalHMMstates). However, HVite typically generates an ```mlf```
during forced alignment that includes only the logical HMM state names. Thus, to use this ```mlf``` in CNTK, either the ```mlf``` must be post-processed to replace the logical state names with thee corresponding senone labels, or HVite must be modified
so that it writes the senone labels directly.

The ```scp``` files processed by the HTKMLFReader can be one of two varieties: either the standard format, where each line corresponds to a physical feature file, or the aliased format, where each line contains a logical name and refers to a segment of a possibly larger physical file, defined by start and end frames. The logical name is used to identify the corresponding labels in the ```mlf``` file. Even if the files are stored individually, as in the first case, the aliased format must always be used with the `blockRandomize` read method, as it uses the information about the starting and ending frames in the ```scp``` file to determine the utterance lengths without having to open the files themselves. In this case, the start frame should be 0 and the end frame should be equal to the length of the utterance minus 1. In addition, for multiple inputs and/or outputs, the aliased format should also be used so that all ```scp``` files and ```mlf``` files have their logical names in common. If the `rollingWindow` read method is used instead of `blockRandomize`, then the start and end frame information may be omitted.

Here are example snippets of ```scp``` and ```mlf``` files for the TIMIT corpus for appropriate for a network with 2 feature inputs (in this case, MFCC and PLP features) and 1 output corresponding to phoneme states.

The ```scp``` file lists all files you to process, using this syntax:

```
id=pathname
```

A CNTK-proprietary extension of this format (that is not found in the original HTK) is that CNTK allows a more convenient relative pathname syntax when the ```scp``` file is located next to the features:

```
id=.../filename
```

where ```...``` refers to the directory of the ```scp``` file.

The ```scp``` file for the MFCC features contains entries such as these.

`train-dr1-fcjf0-si1027.mfc=//hostname/data/TIMIT/mfc/train/dr1/fcjf0/train-dr1-fcjf0-si1027.mfc[0,306]`

`train-dr1-fcjf0-si1657.mfc=//hostname/data/TIMIT/mfc/train/dr1/fcjf0/train-dr1-fcjf0-si1657.mfc[0,281]`

`train-dr1-fcjf0-si648.mfc=//hostname/data/TIMIT/mfc/train/dr1/fcjf0/train-dr1-fcjf0-si648.mfc[0,359]`

The ```scp``` file for the PLP features is similar but point to different physical files. Note that the logical root name in both ```scp``` files is the same.

`train-dr1-fcjf0-si1027.plp=//hostname/data/TIMIT/plp/train/dr1/fcjf0/train-dr1-fcjf0-si1027. plp[0,306]`

`train-dr1-fcjf0-si1657.plp=//hostname/data/TIMIT/plp/train/dr1/fcjf0/train-dr1-fcjf0-si1657. plp[0,281]`

`train-dr1-fcjf0-si648.plp=//hostname/data/TIMIT/plp/train/dr1/fcjf0/train-dr1-fcjf0-si648.plp [0,359]`

An ```mlf``` file lists the labels, using this syntax:

```
#!MLF!#
"id"
labelBegin1 labelEnd1 label1
labelBegin2 labelEnd2 label1

...

.
"id"
labelBegin1 labelEnd1 label1
labelBegin2 labelEnd2 label1

...

.

...
```

Here we have a section terminated with ```.``` (dot) per each input file, and one line per token within each section. The label times are given in a time base of ```10e-7```, and speech frames are normally ```10e-2```, so  ***5 zeroes*** are needed to appended to every time index. 

It is important to notice, that CNTK reads **only first three columns*** within a section of an ```mlp``` file and ignores the rest. In our example ```mlf``` file also shares the logical name with both ```scp`` files. Here is the snippet:

```
#!MLF!#
"train-dr1-fcjf0-si1027.rec"
0 200000 h#_s2 -136.655975 h# -589.680481 h#
200000 400000 h#_s3 -145.780716
400000 800000 h#_s4 -307.243774
800000 1200000 q_s2 -349.529327 q -897.429504 q
1200000 1500000 q_s3 -280.568817
1500000 1800000 q_s4 -267.331390
1800000 1900000 iy_s2 -76.825096 iy -673.892883 iy
1900000 2400000 iy_s3 -305.832458
2400000 2800000 iy_s4 -291.235352
```

Now we will describe *Archive* files, that are sometimes also known as ```chunk``` files. CNTK uses a concept of *chunk* for all of its Readers, and it means an absolutely different thing, so to avoid the confusion we do **not** use the term *chunk* in relation to the files described below, but rather call them *Archives*. 

Archive files are basically column-major matrices of ```float32```, with a 12-byte header that contains the sample size and the number of samples. Generally they are easier to use, especially as a start. The expected header of the file is defined via the ```struct``` below:

```
struct fileheader
{
    int nsamples;
    int sampperiod;
    short sampsize;
    short sampkind;
}
```

where:

* ```sampsize``` is the size of a vector in bytes (```=4 * feature dimension```);
* ```sampkind``` is a numeric identifier of the feature type (MFCC, PLP etc.). CNTK ignores this. If your files are not created by HTK, you can just set this to 9 (USER). And
* ```sampperiod``` should be ```100000``` (CNTK mostly ignores this value, except possibly for error messages).

Finally, it is important to note that in most speech recognition applications, the continuous-valued features are used as the inputs while discrete categorical labels are used as the output. However, the HTKMLFReader just associates the data with the node
names and is agnostic as to how this data is used. For example, an appropriate ```mlf``` file of speaker identity labels could be used to generate a one-hot vector of speaker identity features as an input to the network.
