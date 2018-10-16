---
title:   BrainScript CNTK  Binary Reader
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   07/31/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# BrainScript CNTK Binary Reader

The CNTKBinaryReader (later simply **binary reader**) is designed to be used with large data corpora formatted according to the specification below. It supports the following main features:
* Multiple input streams (inputs) per file
* Both sparse and dense inputs
* Variable length sparse sequences

The [Scripts/ctf2bin.py](https://github.com/Microsoft/CNTK/tree/release/latest/Scripts/ctf2bin.py) script can be used to convert data from the CNTKTextFormat into the CNTKBinaryFormat. Alternatively, one can implement the schema defined below.

## Input Schema

The CNTK binary format (aka **CBF**) is composed of 3 major sections:

1. Prefix
2. Data
3. Header

Each section is concatenated together into one contiguous file.

### Prefix

Prefix is the first 12 bytes of a CNTK binary file containing the following segments:

* unsigned long long (8 bytes), CBF magic number `0x636e746b5f62696e`
* unsigned int (4 bytes), CBF version number, the current version number is `1`.

All numbers in CBF are stored using little-endian byte order.

### Data

The data section contains a collection of fixed-size binary chunks. Each chunk is consumed by the reader all at once (the entire chunk is read into memory and stays there until all sequences from the chunk have been used up for training). Unlike the CNTKTextFormat, where the reader is able to process chunks of different sizes depending on the configuration parameter, the size of the CBF chunk is fixed upfront when the chunk is created. Therefore, the size should be chosen such that reading a chunk can be done efficiently on the underlying platform (the typical chunks size is 32-64 MBs).

A chunk is constructed by serializing all sequences for each input stream, with the order of inputs identical across chunks (e.g., all chunks having all sequences from the 'features' input followed by all sequences from 'labels'). Note that this means that the inputs with different storage formats can be serialized in an arbitrary order, depending on preference (e.g., sparse 'features' and dense 'labels'). 

The CBF chunk is structured as follows (`S` denotes the number of sequence in a given chunk, `I` denotes the number of input streams):

* unsigned int (4 bytes), **(meta) number of samples** in Sequence<sub>**s**</sub>, for `s = 1,...,S`
* **sequence data** for stream Sequence<sub>**i,s**</sub> for `i = 1,...,I; s = 1,...,S`

Since a sequence can contain different number of samples in each input stream, the number of samples here is interpreted as a meta-information about the sequence, which is not required for the reading per se, and will be provided to the higher level components (randomizers, trainers, etc.). This number can, for example, be set to the maximum number of samples across all inputs or it can be set equal to the number of samples in the input that is meant to define the minibatch size (e.g., 'labels').

At the moment, CBF supports two types of sequence data: **dense** and **sparse**. The sequence data type reflects the `storage type` of the corresponding input stream (i.e., all sequence from an input share the same type).

##### Dense Stream Sequence Data

* unsigned int (4 bytes), the actual number of samples in the sequence in this input stream (`N`)
* contiguous array of `sample dim` `element type` values for Sample<sub>**n**</sub> for `n = 1,...,N`

The `element type` can either be float (4 bytes) or double (8 bytes), `sample dim` is the size (the number of elements) of a sample in this input. Both are specified on the per-input basis at the file construction time and recored in the [Header section](#header) described below. For example, a dense sequence with 4 samples from an input that was declared to have `element type = float` and `sample dim = 3` will consume 52 bytes and will be laid out as follows:

| 4 bytes |    12 bytes   |    12 bytes   |    12 bytes   |    12 bytes   |
|---------|---------------|---------------|---------------|---------------|
|   3     | 0.1, 0.2, 0.3 | 0.4, 0.5, 0.6 | 0.7, 0.8, 0.9 | 1.0, 1.1, 1.2 |

##### Sparse Stream Sequence Data

* unsigned int (4 bytes), the actual number of samples in the sequence in this input stream (`N`)
* signed int (4 bytes), total number of non-zero elements in this sequence (`NNZ`)
* contiguous array of `NNZ` `element type` values -- all non-zero element from all `N` samples concatenated together
* contiguous array of `NNZ` signed int values -- indices for all non-zero element from all `N` samples concatenated together (with each individual index in `[0, sample dim)` interval)
* contiguous array of `N` signed int values -- number of non-zero elements for each of the `N`  samples in the sequence.

The sparse format is somewhat similar to the [CSC format](http://docs.nvidia.com/cuda/cusparse/#compressed-sparse-column-format-csc) (with samples interpreted as columns). Please note that  NNZ counts and indices are written out as signed int, since this is the type CNTK uses natively for sparse matrix indices. A sparse sequence from an input declared to have `element type = double` and `sample dim = 1000` having two samples (`[123:0.1, 456:0.2, 789:0.3]` and `[99:0.4, 999:0.5]`) will be laid out as follows:

| 4 bytes | 4 bytes |        40 bytes         |         20 bytes       | 8 bytes |
|---------|---------|-------------------------|------------------------|---------|
|   2     |   5     | 0.1, 0.2, 0.3, 0.4, 0.5 | 123, 456, 789, 99, 999 |  3, 2   |


### Header

The header is placed at the end of the file, after the [Data section](#data), because it includes the offset table that contains the binary offset for each chunk in the file and is populated at the same time as the chunks are written out. The header is structured as follows:

* unsigned long long (8 bytes) sentinel `0x636e746b5f62696e` (same magic number used in the [Prefix](#prefix))
* unsigned int (4 bytes), number of chunks (`C`)
* unsigned int (4 bytes), number of input streams (`I`)
* **stream header** for Stream<sub>**i**</sub>, for `i = 1,...,I`
* **chunk header** for Chunk<sub>**c**</sub>, for `c = 1,...,C` (this is the offset table mentioned above)
* signed long long (8 bytes), offset of the header, 8 bytes starting at this offset has to correspond to the sentinel value.

##### Stream Header

* 1 byte, `storage type` (`0 = dense`, `1 = sparse`)
* length-prefixed (4 bytes) ASCII encoded string, name of the input stream (e.g., `8'features'`, `6'labels'`, etc.)
* 1 byte, `element type` (`0 = float`, `1 = double`)
* unsigned int (4 bytes), `sample dim` -- number of elements in a sample in this input stream (i.e., for sparse, the maximum number). 

##### Chunk Header (offset table)

* signed long long (8 bytes), start offset of the chunk
* unsigned int (4 bytes), number of sequences in the chunk
* unsigned int (4 bytes), total number of samples in the chunk (aggregated across all sequences). As is the case in the [Data section](#data), this number is meta-information required by the randomizer.


## `reader` section

| Parameter | Mandatory | Accepted values | Default value | Description |
|:----------|:---------:|-----------------|---------------|-------------|
| `readerType` | Yes | one of the supported CNTK readers | | Specifies the reader flavor to load (e.g., `CNTKBinaryReader`) |
| `file` | Yes | File path | | Path to the file containing the input dataset (Windows or Linux style) |
| `randomize` | No | `true`, `false` | `true` | Specifies whether the input should be randomized |
| `randomizationSeed` | No | Positive integer | `0` | Initial randomization seed value (incremented every sweep when the input data is re-randomized). |
| `randomizationWindow` | No | Positive integer | | Specifies the randomization range (in number of samples)<sup>[1](#footnote1)</sup>. This controls how much of the dataset resides in memory.   |
| `traceLevel` | No | `0`, `1`, `2` | `1` | Output verbosity level. `0` - show only errors; `1` - show errors and warnings; `2` - show all output<sup>[2](#footnote2)</sup> |
| `keepDataInMemory` | No | `true`, `false` | `false` | If `true`, the whole dataset will be cached in memory |

<a name = "footnote1"><sup>1</sup></a> If no `randomizationWindow` is specified, the randomization range is set to be equal to the size of the dataset (i.e., the input is randomized across the whole dataset). `randomizationWindow` is ignored when `randomize` is set to `false`.

<a name = "footnote2"><sup>2</sup></a> In order to force the reader to show a warning for each input error it ignores (in the sense of not raising an exception), non-default `maxErrors` value should be used in combination with the  `traceLevel` set to `1` or above.

##### `input` sub-section

`input` combines a number of individual inputs, each with an appropriately labeled configuration sub-section. All parameters described below are specific to an *Input name* sub-section associated with a particular input.

| Parameter | Mandatory | Accepted values | Description |
|:----------|:---------:|-----------------|-------------|
| `alias` | No | String | A way of renaming an input in a converted binary file into a new stream for use in the network. |

The `alias` parameter is mainly used in case one has an already created binary file that one wants to use with an already created network. In such a case if the input names do not match, one would have to regenerate the binary file (or at least rewrite the header) in order to use the two pieces together. Instead, the binary reader offers the ability to rename streams in the input into new output streams via the use of the `alias` parameter. In such a case the input stream named by the `alias` parameter is renamed, and will be mapped to a new output stream.
