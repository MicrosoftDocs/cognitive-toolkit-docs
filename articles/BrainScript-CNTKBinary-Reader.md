---
title:   BrainScript CNTK  Binary Reader
author:    chrisbasoglu
date:    03/15/2017
ms.author:   cbasoglu
ms.date:   03/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript CNTK  Binary Reader

The CNTKBinaryReader (later simply *binary reader*) is designed to be used on large data corpuses formatted according to the specification below. It supports the following main features:
* Multiple input streams (inputs) per file
* Both sparse and dense inputs
* Variable length sparse sequences

The Scripts/ctf2bin.py script can be used to convert data in the CNTKTextFormat into the CNTKBinaryFormat. Alternatively, one can implement the schema defined below.

## Input Schema

The binary format is composed of 3 major sections:

1. header
2. offsets table
3. data

Each section is concatenated together into one contiguous file.

### Header

The header contains information regarding the data in the file. It enables the binary file to be used with minimal information provided in the config. Below is the format of the header.

| count | type | name |
|-------|------|------|
| 1     | int64_t | version number |
| 1     | int64_t | number of chunks |
| 1     | int32_t | number of inputs |
| numInputs | matHeader | input header |

version number is the version of the binary format that the file was created for. The current version number is 1.

number of chunks corresponds to the number of chunks (defined below) in the file.

number of inputs corresponds to the number of input streams in the file.

#### matHeader

matHeader is a compound type describing the required information for each input stream. It is defined as:

| count | type | name |
|-------|------|------|
| 1     | int32_t | len |
| len   | char | name |
| 1     | int32_t | deserializer |
| 1     | deserializer | deserializer header data |

len is the number of characters in the name variable.

name is the name of the stream. It is the same name that one will use as the name of the input node in the BrainScript specification.

deserializer is the deserializer that should be used to deserialize the binary data stored on disk. Currently there are two deserializers:
* DenseBinaryDataDeserializer
* SparseBinaryDataDeserializer

Each deserializer requires certain input parameters. These parameters are packed immediately after the deserializer type. Below are the specifications for the header data for the deserializers defined above.

##### DenseBinaryDataDeserializer Header

| count | type | name |
|-------|------|------|
| 1     | int32_t | elemType |
| 1     | int32_t | sampleSize |

elemType is 0 if the values are floats, and 1 if the values are dobules.

sampleSize is the size (i.e., number of columns) in one input sample.

##### SparseBinaryDataDeserializer Header

| count | type | name |
|-------|------|------|
| 1     | int32_t | storageType |
| 1     | int32_t | elemType |
| 1     | int32_t | isSequence |
| 1     | int32_t | sampleSize |

storageType is the storage type of the matrix. Currently only sparse CSC (with value 0) is supported.

elemType is 0 if the values are floats, and 1 if the values are dobules.

isSequence is 0 if the input is not a sequence, and 1 if the input is a sequence.

sampleSize is the size (i.e., number of columns) in one input sample.


### Offsets Table

In the binary format, the data section is broken into chunks. The chunks should be sized such that reading one chunk can be done efficiently on the underlying platform. Since the chunks can be variable sized, an offsets table is required to indicate the starting position of each chunk in the data section. The offsets table also contains the number of sequences, and the number of samples in the chunk for efficient retrieval. Below is the specification for the offsets table:

| count | type | name |
|-------|------|------|
| numChunks | offsets table row | offsets row |

numChunks is the number of chunks defined in the header. offsets table row is a compound type defined as:

| count | type | name |
|-------|------|------|
| 1     | int64_t | offset | 
| 1     | int32_t | numSequences | 
| 1     | int32_t | numSamples |

offset is the number of bytes from the start of the data portion that the chunk begins.

numSequences is the number of sequences in the chunk.

numSamples is the number of samples in the chunk. This is defined as the sum of the number of samples in each sequence. The number of samples in a sequence is defined as the maximum number of samples over all inputs for that sequence.

### Data

The data portion contains the data outlined in chunks as described above. It is constructed by serializing all of the sequences for each input, in the order the inputs were defined in the header. Note that this means that the input formats can be serialized in an arbitrary order, depending on preference.

| count | type | name |
|-------|------|------|
| numInputs | serialized input | data |

#### Serialized Input

Input is serialized such that the deserializer can read the data from the chunk. Below are the serialization specifications for the deserialer defined in the [matHeader](#matHeader) section.

##### DenseBinaryDataDeserializer Data

The DenseBinaryDataDeserializer's data is serialized as each sequence concatenated consecutively in memory. Specifically:

| count | type | name |
|-------|------|------|
| numSequences | elemType[sampleDim] | data |

where:

numSequences is the number of sequences in the chunk (defined in the offsets table)

elemType[sampleDim] is the data encoded as an array of length sampleSize of type elemType, where sampleSize and elemType are defined in the header.

##### SparseBinaryDataDeserializer Data

The SparseBinaryDataDeserializer's data is serialzed as a large matrix in [CSC format](http://docs.nvidia.com/cuda/cusparse/#compressed-sparse-column-format-csc), with an added caveat of how row indices are packed (see below). Before continuing, it is recommended that one reads over the specification on the page linked above.

The data is therefore serialized as:

| count | type | name |
|-------|------|------|
| 1 | int32_t | nnz |
| 1 | elemType[nnz] | vals |
| 1 | int32_t[nnz] | row indices |
| 1 | int32_t[numSequences+1] | condensed column indices |

where:

nnz are the number of non zero values in the chunk

vals is a float array of length nnz containing the values for each element.

row indices is an int32_t array of length nnz containing the row indices for each element.

condensed column indices is an array of int32_t of length (numSequences+1) (where numSequences is defined in the offsets table) containing the offset that each sequence begins.

In order to pack variable length sequences, we make one change to the definition of the row index. 
In particular, each <row index[i]> is encoded as follows:

`new row index[i] = <sample number> * sampleSize + <row index[i]>`

Where:

sample number is the (0 indexed) sample number for the value. Thus the first sample would have a sample number of 0, the second of 1, etc.

sampleSize is the width of a sample as defined in the header

row index[i] is the original row index for the element.

Thus one can decode the sample for each element by dividing it by the sampleSize. Similarly, one can determine the actual row index by modding it with the sampleSize.

##### ```reader``` section

| Parameter | Mandatory | Accepted values | Default value | Description |
|:----------|:---------:|-----------------|---------------|-------------|
| ```readerType``` | Yes | one of the supported CNTK readers | | Specifies the reader flavor to load (e.g., ```CNTKBinaryReader```) |
| ```file``` | Yes | File path | | Path to the file containing the input dataset (Windows or Linux style) |
| ```randomize``` | No | ```true```, ```false``` | ```true``` | Specifies whether the input should be randomized |
| ```randomizationSeed``` | No | Positive integer | ```0``` | Initial randomization seed value (incremented every sweep when the input data is re-randomized). |
| ```randomizationWindow``` | No | Positive integer | | Specifies the randomization range (in number of samples)<sup>[1](#footnote1)</sup>. This controls how much of the dataset resides in memory.   |
| ```traceLevel``` | No | ```0```, ```1```, ```2``` | ```1``` | Output verbosity level. ```0``` - show only errors; ```1``` - show errors and warnings; ```2``` - show all output<sup>[2](#footnote2)</sup> |
| ```keepDataInMemory``` | No | ```true```, ```false``` | ```false``` | If ```true```, the whole dataset will be cached in memory |

<a name = "footnote1"><sup>1</sup></a> If no `randomizationWindow` is specified, the randomization range is set to be equal to the size of the dataset (i.e., the input is randomized across the whole dataset). `randomizationWindow` is ignored when `randomize` is set to ```false```.

<a name = "footnote2"><sup>2</sup></a> In order to force the reader to show a warning for each input error it ignores (in the sense of not raising an exception), non-default `maxErrors` value should be used in combination with the  `traceLevel` set to `1` or above.

##### ```input``` sub-section

```input``` combines a number of individual inputs, each with an appropriately labeled configuration sub-section. All parameters described below are specific to an *Input name* sub-section associated with a particular input.

| Parameter | Mandatory | Accepted values | Description |
|:----------|:---------:|-----------------|-------------|
| ```alias``` | No | String | A way of renaming an input in a converted binary file into a new stream for use in the network. |

The ```alias``` parameter is mainly used in case one has an already created binary file that one wants to use with an already created network. In such a case if the input names do not match, one would have to regenerate the binary file (or at least rewrite the header) in order to use the two pieces together. Instead, the binary reader offers the ability to rename streams in the input into new output streams via the use of the ```alias``` parameter. In such a case the input stream named by the ```alias``` parameter is renamed, and will be mapped to a new output stream.