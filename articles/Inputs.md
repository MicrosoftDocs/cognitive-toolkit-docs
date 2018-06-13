---
title:   Inputs
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   01/14/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Input{}

Defines an input that is fed by a reader.

    Input (shape, sparse=false, dynamicAxis=DefaultAxis, tag='feature')

## Parameters

* `shape`: scalar (if the input is a vector) or tensor shape (if the input is multidimensional). E.g. `40` for 40-dimensional features, or `(640:480:3)` for VGA-sized color images.
* `sparse` (default: false): if true then input is stored as sparse matrix
* `dynamicAxis` (default: None): alternative dynamic axis to be used by this input

## Return value

A variable that receives its value from a `reader` stream of the same name.

### Description

`Input{}` declares a variable that represents input read from a reader.
Inputs must be declared at the outermost level of the `BrainScriptNetworkBuilder` section,
and the `reader` section must define a stream with the same name.

#### Sparse input data

The `sparse=true` option declares that the input data shall be represented as a sparse vector.
This is commonly used for reading categorical data (such as words) represented by one-hot vectors.
Sparse inputs have two common uses:
* inputs that get embedded; and
* labels.

An embedding is a collection of dense continuous-valued vectors
indexed by the numeric id of a word (or other categorical item).
In CNTK, this index operation is expressed as a matrix multiplication
with a one-hot representation of the word.
For example the second word in the dictionary would be represented by
a column vector `w = (0, 1, 0, 0, ..., 0)^T`,
and the matrix product `E * w` is equivalent to retrieving the second column of
a matrix `E`, where the columns of `E` are the embedding vectors.

Formally, this matrix product multiplies with a lot of zeroes,
but if `w` is represented in sparse format, this operation indeed
just amounts to an indexing operation, so this is cheap.

Note that input data of rank>1 cannot be represented in sparse format.

### Sparse labels
Like sparse inputs, word or other categorical labels can be represented in sparse form.

Note, however, that sparse labels are currently not supported by the direct form of
the [`CrossEntropyWithSoftmax()`](./Loss-Functions-and-Metrics.md#crossentropy-crossentropywithsoftmax).
Instead, a manually-written form must be used, as [given here](./Loss-Functions-and-Metrics.md#sparse-labels)

Note that labels data of rank>1 cannot be represented in sparse format.

### Image inputs
Images are read as rank-3 tensors of tensor dimensions `[W x H x C]` with width `W`, height `H`,
and number of color channels `C` (3 for RGB or 1 for B&W).

### Variable-length sequences
Inputs can be either individual samples (e.g. images) or sequences of samples (e.g. sentences).
For CNTK, these are the same (an individual sample is a sequence of length 1).
It is up to the specific input data and reader whether an input receives individual samples or sequences.

## Examples

### 40-dimensional speech feature vectors

    features = Input{40}

To read a whole window including 10 left and 10 right neighbor frames:

    features = Input{(40:21)}

### Word sequences, vocabulary size 33124

    words = Input{33124, sparse=true}

with corresponding reader definition (to read them from a CTF file):

    reader = {
        readerType = "CNTKTextFormatReader"
        file = "my_text_corpus.ctf"
        randomize = true
        input = { words = { alias = "w" ; dim = 33124 ;  format = "sparse" }}
    }

### CIFAR-10 Images

    images = Input{(32:32:3)}
    labels = Input{10}

with corresponding reader definition:

    reader = {
        verbosity = 0 ; randomize = true
        deserializers = ({
            type = "ImageDeserializer" ; module = "ImageReader"
            file = "$dataDir$/cifar-10-batches-py/train_map.txt"
            input = {
                images = { transforms = (
                    { type = "Crop" ; cropType = "RandomSide" ; sideRatio = 0.8 ; jitterType = "UniRatio" } :
                    { type = "Scale" ; width = 32 ; height = 32 ; channels = 3 ; interpolations = "linear" } :
                    { type = "Transpose" }
                )}
                labels = { labelDim = 10 }
            }
        })
    }
