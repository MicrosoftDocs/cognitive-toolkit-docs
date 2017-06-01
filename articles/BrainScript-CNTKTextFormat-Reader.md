---
title:   BrainScript CNTK Text Format Reader 
author:    chrisbasoglu
date:    03/20/2017
ms.author:   cbasoglu
ms.date:   03/20/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript CNTK Text Format Reader 

This page documents the CNTKTextFormatReader from the point of view of BrainScript, but Python users may learn by reading this document as well -- the concepts, parameters and patterns are all the same.

CNTKTextFormatReader (later simply *CTF Reader*) is designed to consume input text data formatted according to the specification below. It supports the following main features:
* Multiple input streams (inputs) per file
* Both sparse and dense inputs
* Variable length sequences

## CNTK Text Format (CTF)

Each line in the input file contains one sample for one or more inputs. Since (explicitly or implicitly) every line is also attached to a sequence, it defines one or more **<sequence, input, sample>** relations. Each input line must be formatted as follows:

`[Sequence_Id](Sample or Comment)+`

where

`Sample=|Input_Name (Value )*`

`Comment=|# some content`

* Each line starts with a sequence id and contains one or more samples (in other words, each line is an unordered collection of samples).
* Sequence id is a number. It can be omitted, in which case the line number will be used as the sequence id.
* Each sample is effectively a key-value pair consisting of an input name and the corresponding value vector (mapping to higher dimensions is done as part of the network itself).
* Each sample begins with a pipe symbol (`|`) followed by the input name (no spaces), followed by a whitespace delimiter and then a list of values.
* Each value is either a number or an index-prefixed number for sparse inputs.
* Both tabs and spaces can be used interchangeably as delimiters.
* A comment starts with a pipe immediately followed by a hash symbol: `|#`, then followed by the actually content (body) of the comment. The body can contain any characters, however a pipe symbol inside the body needs to be escaped by appending the hash symbol to it (see the example below). The body of a comment continues until the end of line or the next un-escaped pipe, whichever comes first.
 
## Simple Example

This example is based on a minimal set of parameters and format options.

To use the CTF Reader set the `readerType` to `CNTKTextFormatReader` in the reader section of your CNTK configuration:
```
...
reader = [
    readerType = "CNTKTextFormatReader" 
    file = "c:\mydata\SampleInput.txt" # See the second example for Linux path example
    
    # IMPORTANT!
    # All inputs are grouped within "input" sub-section.
    input = [
        A = [
            dim = 5 
            format = "dense"
        ]
        B = [
            dim = 1000000
            format = "sparse"
        ]
        C = [
            dim = 1
            format = "dense"
        ]
    ]
]
# the rest of the cntk config ...
```
(This fragment as well as other NDL examples in this document present only `reader` section, omitting the rest of the CNTK configuration; see the end of this page for pointers to a set of complete example Networks and the corresponding datasets)

The CTF Reader requires the following set of parameters:
* `file` - path to the file with the dataset.
* `input` - sub-section defining *inputs* identified by *input names* (`A`, `B` and `C` in the example above). Within each input the following required parameters must be specified:
	* `format` - specifies the input type. Must either be `dense` or `sparse`
	* `dim` - specifies the dimension of the input value vector (for *dense* input this directly corresponds to the number of values in each sample, for *sparse* this represents the upper bound on the range of possible index values).

The input data corresponding to the reader configuration above should look something like this:

```
|B 100:3 123:4 |C 8 |A 0 1 2 3 4 |# a CTF comment
|# another comment |A 0 1.1 22 0.3 54 |C 123917 |B 1134:1.911 13331:0.014
|C -0.001 |# a comment with an escaped pipe: '|#' |A 3.9 1.11 121.2 99.13 0.04 |B 999:0.001 918918:-9.19
```
Note the following about the input format:
* `|Input_Name` identifies the beginning of each input sample. This element is mandatory and is followed by the correspondent value vector.
* Dense vector is just a list of floating point values; sparse vector is a list of `index:value` tuples.
* Both tabs and spaces are allowed as value delimiters (within *input vectors*) as well as input delimiters (*between inputs*).
* Each separate line constitutes a "*sequence*" of length 1 ("Real" variable-length sequences are explained in the extended example below).
* Each input identifier can only appear once on a single line (which translates into *one sample per input per line* requirement).
* The order of input samples within a line is NOT important (conceptually, each line is an unordered collection of key-value pairs)
* Each well-formed line must end with either a "Line Feed" `\n` or "Carriage Return, Line Feed" `\r\n` symbols.

## Extended Example

This example features all possible configuration parameters and shows various input format options. Please refer to the tables [below](#configuration-parameters) for the full description of the configuration parameters used in this example.

```
...
precision="double"

reader = [
    readerType = "CNTKTextFormatReader" 
    file = "/home/mydata/SampleInput.txt" # See the first example for Windows style path example
    randomize = true
    randomizationWindow = 30
    skipSequenceIds = false
    maxErrors = 100
    traceLevel = 2
    
    chunkSizeInBytes = 1024

    keepDataInMemory = true
    frameMode = false
    
    input = [
        Some_very_long_input_name = [
            alias = "a"
            dim = 3 
            format = "dense"
        ]
        Some_other_also_very_long_input_name = [
            alias = "b"
            dim = 2 
            format = "dense"
        ]
    ]
]
# the rest of the cntk config ...
```
With a composite reader, it would like the following:
```
reader = {
        verbosity = 0 ; 
        randomize = true;
        randomizationWindow=30
        deserializers = ({
            type = "CNTKTextFormatDeserializer" ; module = "CNTKTextFormatReader"
            file = "/home/mydata/SampleInput.txt" # See the first example for Windows style path example
            maxErrors = 100
            skipSequenceIds = false
            traceLevel = 2
            input = {
                qu1fea = {alias = "qui"; dim = 95589; format = "sparse"}
                qu2fea = {alias = "quj"; dim = 95589; format = "sparse"}
                pairweight = {alias = "wij"; dim = 1; format = "dense"}
            }
        })
    }

```


The corresponding input file can then look approximately as follows:

```
100 |a 1 2 3 |b 100 200
100 |a 4 5 6 |b 101 201
100 |b 102983 14532 |a 7 8 9 
100 |a 7 8 9
200 |b 300 400 |a 10 20 30
333 |b 500 100 
333 |b 600 -900
400 |a 1 2 3 |b 100 200
|a 4 5 6 |b 101 201
|a 4 5 6 |b 101 201
500 |a 1 2 3 |b 100 200

```

All options discussed in the example [above](#simple-example), still apply here. On top of that, we introduced two additional features:

### Input name aliases
Input names can be arbitrary long and thus repeating them throughout the input file may not be space-efficient. To mitigate this, the dataset can use "aliases" instead of full input names. Aliases then need to be specified within each input sub-subsection. In our example, the dataset uses aliases `a` and `b`, which are mapped to "Some_very_long_input_name" and "Some_other_also_very_long_input_name" respectively in the reader config section.

 
### Sequence IDs
As already mentioned, each separate line in the input file represents a *sequence* containing a single sample for each input. However, if a line is prefixed with a non-negative number, the number is used as the corresponding *sequence id*. All subsequent lines that share the same sequence id are merged together to become a part of the same sequence. Therefore, repeating the same numerical prefix for N lines allows to build up a multi-sample sequence, with each input containing between 1 and N samples. Omitting the sequence prefix on the second and following lines has the same effect. Thus, the example dataset above defines *five* sequences with ids `100`,  `200`, `333`, `400` and `500`. 

Setting `skipSequenceIds` parameter in the reader section to `true`, forces the reader to ignore all explicit sequence ids in the dataset and treat separate lines as individual sequences. Also, omitting the sequence id on the first line in the dataset has the same effect -- all subsequent sequence ids are ignored, lines treated as individual sequences, as in this example:

```
|a 1 2 3 |b 100 200
100 |a 4 5 6 |b 101 201
200 |b 102983 14532 |a 7 8 9 

```

A few final things to consider when using sequences: 
* Sequence ids must be unique.
* Id prefixes can only be repeated for consecutive lines. 
* Sequence length in lines (that is, the number of lines sharing the same id prefix) must not exceed the maximum input length in samples (the number of samples in an input) in this sequence.

For example, the following datasets are **invalid**:
``` 
100 |a 1 2 3 |b 100 200
200 |a 4 5 6 |b 101 201
100 |b 102983 14532 |a 7 8 9 

123 |a 1 2 3 |b 100 200
456 |a 4 5 6 
456 |b 101 201
```
## A Few Real-World Examples

* Classification: Every line contains a sample, consisting of a label and features. No sequence ID needed, since every line is its own "sequence" of length 1.
```
|class 23:1 |features 2 3 4 5 6
|class 13:1 |features 1 2 0 2 3
...
```
* DSSM: Every line contains a source-target document pair, expressed through a bag of words, encoded as sparse vectors. 
```
|src 12:1 23:1 345:2 45001:1    |tgt 233:1 766:2 234:1
|src 123:1 56:1 10324:1 18001:3 |tgt 233:1 2344:2 8889:1 2234:1 253434:1
```

* Part-of-speech tagging: Sequences mapping every element to a corresponding label. The sequences are aligned vertically (one word + tag per line).
```
0 |word 234:1 |tag 12:1
0 |word 123:1 |tag 10:1
0 |word 123:1 |tag 13:1
1 |word 234:1 |tag 12:1
1 |word 123:1 |tag 10:1
...
```
* Sequence classification: Sequences mapped to a single label. Sequences are aligned vertically; The "class" label can occur in any line that has the same sequenceId.

NOTE: At the moment the number of lines must not exceed the length of the longest sequence. This means that the label cannot appear on a line on its own. This is an implementation detail that will be lifted in the future.
```
0 |word 234:1 |class 3:1
0 |word 123:1
0 |word 890:1
1 |word 11:1 |class 2:1
1 |word 344:1
```
* Sequence to sequence: Map a source sequence to a target sequence. The two sequences are aligned vertically and, in the easiest case, just printed after another. They are joined by having the same overall "sequence ID" (which then becomes a "work unit ID" in this case).

NOTE: At the moment the number of lines must not exceed the length of the longest sequence. This means that sequences must be aligned horizontally. This is an implementation detail that will be lifted in the future.
```
0 |sourceWord 234:1  |targetWord 344:1
0 |sourceWord 123:1  |targetWord 456:1
0 |sourceWord 123:1  |targetWord 2222:1
0 |sourceWord 11:1 
1 |sourceWord 123:1 
...
```
* Learning to Rank: A "sequence" represents a query, every sample a document with a hand-labeled rating. In this case the "sequence" is just a multiset that (in the context of a learning-to-rank loss function) doesn't have an ordering.
```
0 |rating 4 |features 23 35 0 0 0 21 2345 0 0 0 0 0 
0 |rating 2 |features 0 123 0 22 44 44 290 22 22 22 33 0 
0 |rating 1 |features 0 0 0 0 0 0 1 0 0 0 0 0
1 |rating 1 |features 34 56 0 0 0 45 1312 0 0 0 0 0 
1 |rating 0 |features 45 45 0 0 0 12 335 0 0 0 0 0 
2 |rating 0 |features 0 0 0 0 0 0 22 0 0 0 0 0 
...
```

## Configuration Parameters

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `precision`                           | Specifies the floating point precision (`double` or `float`) of the input values. *Optional*, defaults to  `float`. |


### `reader` section

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `readerType`                          | Specifies one of the supported CNTK readers to load (e.g., `CNTKTextFormatReader`). **Required**. |
| `file`                                | Path to the file containing the input dataset (Windows or Linux style). **Required**. |
| `randomize`                           | Specifies whether the input should be randomized (`true`, `false`). *Optional*, defaults to  `true`. |
| `randomizationSeed`                   | Initial randomization seed value (incremented every sweep when the input data is re-randomized). *Optional*, defaults to  `0`. |
| `randomizationWindow`                 | Specifies the size (positive integer) of the randomization window (i.e., randomization range). This parameter affects how much of the dataset needs to reside in memory at once. *Optional*, depending on the `sampleBasedRandomizationWindow` value, defaults either to the size of the whole dataset in samples (i.e., the input is randomized across the whole dataset), or 4GB of disk space worth of chunks (i.e., `128` when the chunk size equals 32MB). This parameter is ignored when `randomize` is `false`. |
| `sampleBasedRandomizationWindow`      | If `true`, the size of the randomization window is interpreted as a certain number of samples, otherwise -- as a number of chunks. *Optional*, defaults to `false`.  Similarly to `randomizationWindow`, this parameter is ignored, when `randomize` is `false`. |
| `skipSequenceIds`                     | If `true`, the reader will ignore sequence IDs in the input file, interpreting each separate line as an independent sequence of size 1 ([see the section on the sequence ids](#sequence-ids)). *Optional*, defaults to `false`. |
| `maxErrors`                           | Number of input errors after which an exception should be raised. *Optional*, defaults to `0`, which means that the first malformed value will trigger an exception. |
| `traceLevel`                          | Output verbosity level. `0` - show only errors; `1` - show errors and warnings; `2` - show all output. *Optional*, defaults to `1`. |
| `chunkSizeInBytes`                    | Number of consecutive bytes to read from disk in a single read operation. *Optional*, defaults to  `33554432` (32MB). |
| `keepDataInMemory`                    | If `true`, the whole dataset will be cached in memory. *Optional*, defaults to `false`. |
| `frameMode`                           | `true` signals the reader to use a packing method optimized for frames (sequences that contain only a single sample). *Optional*, defaults to `false`. |

### `input` sub-section

`input` combines a number of individual inputs, each with an appropriately labeled configuration sub-section. All parameters described below are specific to an *Input name* sub-section associated with a particular input.

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `alias`                               | An alternative shorthand name (string) used to identify the input in the dataset. *Optional* |
| `format`                              | Specifies input type (`dense`, `sparse`). **Required**. |
| `dim`                                 | Dimension (positive integer) of the input value (i.e., the number of input values in a sample for *dense* input, the upper bound on the index range for *sparse* input). **Required**. |
| `definesMBSize`                       | Flag (default false), indicating whether the minibatch size should be counted in samples from this particular stream *Optional*. |


You will find [complete network definitions](https://github.com/Microsoft/CNTK/tree/master/Tests/UnitTests/ReaderTests/Config/CNTKTextFormatReader) and [the corresponding data set examples](https://github.com/Microsoft/CNTK/tree/master/Tests/UnitTests/ReaderTests/Data/CNTKTextFormatReader) in the CNTK Repository. There, you will also find an [End-to-End test](https://github.com/Microsoft/CNTK/tree/master/Tests/EndToEndTests/ParallelTraining) that uses the CNTKTextFormat reader.
