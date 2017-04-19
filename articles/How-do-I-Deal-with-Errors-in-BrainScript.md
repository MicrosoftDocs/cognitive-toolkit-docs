* [Deal with the error "No node named 'x'; skipping"](./How-do-I-Deal-with-Errors-in-BrainScript#deal-with-the-error-no-node-named-x-skipping)? 
* [Avoid the "AddSequence: Sequences must be a least one frame long." exception in sequence to sequence](How-do-I-Deal-with-Errors-in-BrainScript#avoid-addsequence-exception)? 
* [Deal with the "No Output nodes found" error](./How-do-I-Deal-with-Errors-in-BrainScript#deal-with-the-no-output-nodes-found-error)? 
* [Deal with the error "Reached the maximum number of allowed errors"](./How-do-I-Deal-with-Errors-in-BrainScript#deal-with-the-error-reached-the-maximum-number-of-allowed-errors)? 
* [Deal with "InputValue operation had its row dimension x changed by the reader to y"](./How-do-I-Deal-with-Errors-in-BrainScript#deal-with-inputvalue-operation-had-its-row-dimension-x-changed-by-the-reader-to-y-compatible-dimensions-in-reader-and-config)? 
* [Avoid the error CURAND failure 201](./How-do-I-Deal-with-Errors-in-BrainScript#avoid-the-error-curand-failure-201)? 

## Deal with the error 'No node named 'x'; skipping'

This can happen for example when you use the `"write"` action for certain values of `outputputNodeNames`. The reason is that `outputNodeNames` expects the name of a node in the computational network which is not necessarily the same as the name of the expression in BrainScript. Check your log to see all the node names and use the appropriate node name as the right hand side of `outputputNodeNames`

## Avoid AddSequence Exception

Suppose you are training a sequence to sequence model for translating from English to Hungarian with inputs like
```
<s> I will not buy this record, it is scratched <s/> Nem fogok vásárolni ezt a lemezt, azt karcos </s>
<s> My hovercraft is full of eels <s/> A légpárnás hajóm tele van angolnákkal </s>
...
```
using the LUSequenceReader. There's a chance you'll encounter the cryptic exception
```
EXCEPTION occurred: AddSequence: Sequences must be a least one frame long.
```
The way to avoid it is to ensure that the `<s/>` token is in your vocabulary and its position corresponds to the variable `separatorRow` (0-based) in your config file.

We also recommend to not use LUSequenceReader, but instead convert your text using the
[`txt2ctf.py`](https://github.com/Microsoft/CNTK/blob/master/Scripts/txt2ctf.py) tool
and then reading it with the [`CNTKTextFormatReader`](CNTKTextFormat-Reader).

## Deal with the 'No Output nodes found' error

Remember that Brainscript is case sensitive. So code like 
```
# Special Nodes
OutputNodes = (ol)
```
will not work as expected. The names of all special nodes are *lower* camelCase
```
# Special Nodes
featureNodes = (features)
labelNodes = (labels)
criterionNodes = (ce)
evaluationNodes = (errs)
outputNodes = (ol)
```

## Deal with the error 'Reached the maximum number of allowed errors'

The CNTKTextFormatReader can tolerate a `maxErrors` number of malformed lines (default is 0). The error message 
> Reached the maximum number of allowed errors while reading the input file

means that that the reader has encountered more than `maxErrors` malformed lines. Make sure you have generated the input file correctly (e.g. there are no stray vertical bars in comment streams (`|#`)) or increase `maxErrors` if recreating the input file is close to impossible.

## Deal with "InputValue operation had its row dimension x changed by the reader to y" (Compatible dimensions in reader and config)

There are two typical cases where you might see this error message. The underlying reason is that the reader config and the training config do not agree. 

The first typical case is that when you modify a working config file to change the dimensionality of the output (e.g. take a config that works for a 7 class problem and make a new one that should work for a 5 class problem) you may encounter the error message 
```
NotifyFunctionValuesMBSizeModified: labels InputValue operation had its row dimension 7 changed by the reader to 5
```
This is most likely due to only updating the network definition part of your config file but not your reader definition.
Please double-check:

* all [`Input{}`](./Inputs#input) nodes have corresponding streams in the reader section with the same name
* the dimensions match

A second case is when you provide a wrong hint to the reader by saying 
```
featureNodes = (z)
```
and `z` is not an actual input but an expression.

## Avoid the error CURAND failure 201

You are probably running on a Pascal GPU (for example a GTX 1080) and are using a Microsoft Cognitive Toolkit compiled with CUDA 7.5.

Starting with CNTK 2 Beta 5 the Window binary packages are generated using NVIDIA CUDA 8 and will not show this problem anymore. Please update you local CNTK version.
