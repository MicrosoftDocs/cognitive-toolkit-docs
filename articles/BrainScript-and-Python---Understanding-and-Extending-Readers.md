---
title:   BrainScript and Python Understanding and Extending Readers
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript and Python: Understanding and Extending Readers

Starting from version 1.5, CNTK is moving away from the monolithic reader design towards a more composable model that allows you to specify
and compose input data of different formats.

Before, each and every reader was responsible for different aspects of data reading, including but not limited to:
* Deserialization of the data from external storage into in-memory representation
* Randomization of the whole corpus
* Different transformations of input sequences/samples (i.e. cropping or scaling of images)
* Creation of minibatches for different modes (i.e. frame, sequence or truncated BPTT) with a layout that can be consumed by GPU
* Prefetch on the level of minibatches and IO chunks

In version 1.5, the major pieces of the above functionality were factored out and moved to core CNTK to be shared between different readers.
This version also introduces two main abstractions that can be extended in order to support new data formats:
* **deserializer** - it is responsible for deserialization of input from external storage into in-memory sequences
* **transform** - it transforms an input sequence into an output sequence

In the next sections we discuss these abstractions in more detail.

## Configuring a reader (minibatch source) in Python

This section provides several examples on how a composite reader (aka [MinibatchSource](https://cntk.ai/pythondocs/cntk.io.html#cntk.io.MinibatchSource)) can be configured in Python.

The following example was adapted from [AlexNet_ImageNet_Distributed.py](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Image/Classification/AlexNet/Python/AlexNet_ImageNet_Distributed.py), it shows the Python equivalent of the AlexNet reader from [Transforms](#transforms) section.

```
import cntk.io

mean_file = ...
map_file = ...

# model dimensions
image_height = 227
image_width  = 227
num_channels = 3  # RGB
num_classes  = 1000

transforms = [
     ImageDeserializer.crop(crop_type='randomside', 
                            side_ratio=0.88671875, 
                            jitter_type='uniratio'),
     ImageDeserializer.scale(width=image_width, 
                            height=image_height, 
                            channels=num_channels, 
                            interpolations='linear'),
     ImageDeserializer.mean(mean_file)
]

reader = MinibatchSource(
    ImageDeserializer(map_file, StreamDefs(
        # first column in map file is referred to as 'image'
        features = StreamDef(field='image', transforms=transforms),
        # and second as 'label' 
        labels   = StreamDef(field='label', shape=num_classes)))) 
```

The following example (adapted from [A2_RunCntk_py3.py](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Image/Detection/FastRCNN/A2_RunCntk_py3.py)) shows how several deserializers can be combined together. 

```
n_rois = 100
n_classes = 17
rois_dim = 4 * n_rois
label_dim = n_classes * n_rois

map_file = ...
roi_file = ...
label_file = ...

# read images
scale = ImageDeserializer.scale(width=1000, 
                                height=1000, 
                                channels=3,
                                scale_mode="pad", 
                                pad_value=114, 
                                interpolations='linear')
image_source = ImageDeserializer(map_file)
image_source.ignore_labels()
image_source.map_features('features', [scale])

# read rois and labels
roi_source = CTFDeserializer(roi_file)
roi_source.map_input('rois', dim=rois_dim, format="dense")
label_source = CTFDeserializer(label_file)
label_source.map_input('roiLabels', dim=label_dim, format="dense")

# define a composite reader
reader = MinibatchSource([image_source, roi_source, label_source])

...

# define mapping from reader streams to network inputs
input_map = {
    image_input: reader.streams.features,
    roi_input: reader.streams.rois,
    label_input: reader.streams.roiLabels
}
```

# BrainScript

## Deserializers
Let's have a look at the following fragment of configuration for the *HTKMLFReader* from the end-to-end *LSTM/FullUtterance* test
(full config [here](https://github.com/Microsoft/CNTK/tree/release/latest/Tests/EndToEndTests/Speech/LSTM/cntk.cntk)):
```
...
# Old reader config. For illustration only.
reader = [
    readerType = "HTKMLFReader"
    readMethod = "blockRandomize"
    nbruttsineachrecurrentiter = 32
    randomize = "auto"
    verbosity = 0

    features = [
        dim = 363
        type = "real"
        scpFile = "$DataDir$/glob_0000.scp"
    ]

    labels = [
        mlfFile = "$DataDir$/glob_0000.mlf"
        labelMappingFile = "$DataDir$/state.list"

        labelDim = 132
        labelType = "category"
    ]
]
```

This fragment of configuration declares a reader that produces two streams of data with names `"features"` and `"labels"`. It takes as input two types of files:
* a list of feature files known in [HTK](http://htk.eng.cam.ac.uk/) parlance as an `scp` file (“script” file)
* a label file known as `mlf` file (“master label file”)

In the above configuration fragment there are no explicit entities that would define how `scp` or `mlf` formats are deserialized.
Everything is encapsulated in the *HTKMLFReader* configuration.
So if you need to expose yet another input stream of different data format together with `scp` and `mlf`, you would need to change *HTKMLFReader* and add support there.

To increase composability and reuse, the *new* configuration for the same input explicitly defines deserializers and the input streams that they produce:
```
reader = [
    verbosity = 0
    randomize = true

    # A list of deserializers the reader uses.
    deserializers = (
        [
            # Type of deserializer, in this case the one that knows
            # how to deserialize HTK feature files.
            type = "HTKFeatureDeserializer"
            # Module (.dll or .so) where this deserializer is implemented
            module = "HTKDeserializers"

            # Description of input streams the deserializer provides,
            # can be one or many, depending on a particular
            # deserializer implementation
            # For HTKFeatureDeserializer, just one stream can be described.
            input = [
                # Description of input stream to feed the Input node named "features"
                features = [
                    dim = 363
                    scpFile = "$DataDir$/glob_0000.scp"
                ]
            ]
        ]:
        [
            # Type of deserializer, in this case the one
            # that knows how to deserialize mlf files.
            type = "HTKMLFDeserializer"
            module = "HTKDeserializers"
            # Description of input streams the deserializer provides,
            # For HTKMLFDeserializer, just one stream can be described.
            input = [
                # Description of input stream to feed the Input node named "labels"
                labels = [
                    dim = 132
                    mlfFile = "$DataDir$/glob_0000.mlf"
                    labelMappingFile = "$DataDir$/state.list"
                    # whether phone boundary information should be encoded
                    # set to true in CTC-type training
                    phoneBoundaries=false
                ]
            ]
        ]
    )
]
```

The sequences produced by the `mlf` and `htk` deserializers are combined together based on their logical key
(which is a string that uniquely identifies a speech utterance and is present in both `scp` and `mlf` files).
When you need another stream of different format, you can simply add the corresponding deserializer to the configuration
(it is not possible with the HTK feature and HTK MLF deserializers right now to expose more than one input stream each).

> [!NOTE]
> Currently, both old and new reader configurations are supported. When the "deserializers" key is used in the reader configuration, the reader type is implicitly set to "CompositeDataReader". To make sure the CompositeDataReader module can be loaded on Windows the `Cntk.Composite.dll` should be located in the same directory as the CNTK executable. On Linux `Cntk.Composite.so` should be in the `lib` folder that sits side-by-side to the `bin` folder containing the CNTK executable.

Currently CNTK supports the below deserializers:

| Deserializer type           | Module               | Description                                                            |
| -----------------           | ------               | -----------                                                            |
| HTKFeatureDeserializer      | HTKDeserializers     | Deserializer for HTK feature files                                     |
| HTKMLFDeserializer          | HTKDeserializers     | Deserializer for HTK MLF files                                         |
| ImageDeserializer           | ImageReader          | Deserializer for images encoded as plain files or in zip archive.      |
| Base64ImageDeserializer     | ImageReader          | Deserializer for images encoded as base64 strings in the mapping file. |
| CNTKTextFormatDeserializer  | CNTKTextFormatReader | Deserializer for CNTK text format files                                |
| CNTKBinaryFormatDeserializer| CNTKBinaryReader     | Deserializer for CNTK binary format files                              |

Please refer to the tables [below](#general-reader-configuration) for the full description of the configuration parameters.

## Transforms
A transform is a simple abstraction that take a sequence as an input, performs some transformation of samples in the sequence and returns the output sequence.
Typical examples of transforms are different transformations of images such as crop, scale or transpose.
Transforms can be configured on per input basis.

Let's have a look how transforms can be applied to the input (the config is taken from the [Tests/EndToEndTests/Image/AlexNet](https://github.com/Microsoft/CNTK/tree/release/latest/Tests/EndToEndTests/Image/AlexNet) test):
```
deserializers = ([
    type = "ImageDeserializer"
    module = "ImageReader"

    # Map file which maps images to labels
    file = "$ConfigDir$/train_map.txt"

    # Description of input streams
    input = [
            # Description of input stream to feed the Input node named "features"
            features = [
                transforms = (
                    [
                        type = "Crop"
                        # Possible values: Center, RandomSide, RandomArea, Multiview10. Default: Center
                        cropType = "RandomSide"
                        # Crop scale side ratio.
                        sideRatio = 0.875
                        # Crop scale ratio jitter type
                        jitterType = "UniRatio"
                    ]:[
                        type = "Scale"
                        width = 224
                        height = 224
                        channels = 3
                        # Interpolation to use when scaling image to width x height size.
                        interpolations = "linear"
                    ]:[
                        type = "Mean"
                        # Stores mean values for each pixel in OpenCV matrix XML format.
                        meanFile = "$ConfigDir$/ImageNet1K_mean.xml"
                    ]:[
                        # Changes the image layout from HWC to CHW
                        type = "Transpose"
                    ]
                )
            ]
            # Description of input stream to feed the Input node named "labels"
            labels = [
                labelDim = 1000
            ]
        ]
    ]
])
```
In this configuration four transforms are applied to the input stream `features`.
Initially, the image data deserializer produces sequences consisting of a single image in HWC representation.
After that the ordered list of transforms is applied to the image: firstly the *Crop* transform, followed by *Scale* and *Mean*.
The last transformation is *Transpose* that changes the image layout from HWC to CHW.

Currently the following transforms are implemented.
For their detailed description please see [ImageReader](./Brainscript-Image-reader.md#crop-transform).

| Transform type | Module      |
| -------------- | ------      |
| Crop           | ImageReader |
| Scale          | ImageReader |
| Color          | ImageReader |
| Mean           | ImageReader |
| Transpose      | ImageReader |

## New Reader Configuration Format Description

A reader configuration section to compose several data deserializers looks like follows:
```
reader = [
    randomize = true|false
    verbosity = 0|1|2
    ...

    deserializers = (
        [<deserializerConfiguration1>]:
        [<deserializerConfiguration2>]:
        ...
        [<deserializerConfigurationN>]
    )
]
```
Each deserializer configuration is specified as:
```
[
    module = "<readerModuleName>"   # Name of the external module (.dll or .so) where this particular deserializer is implemented
    type = "<deserializerType>"     # The type of the deserializer

    # There could be more deserializer-specific options in this section

    # Date deserializer input - describes a set of streams this deserializer produces.
    # It can be one (as in HTK) or many (as in CNTKTextFormat)
    input = [
        # Replace 'InputNameN' by the name of the corresponding input node in the network.
        InputName1 = [<inputConfiguration>]
        InputName2 = [<inputConfiguration>]
        ...
    ]
]
```
An input configuration contains input-specific options and, optionally, an ordered list of transforms that should be applied to the input:
```
[
    # Per-input data deserializer-specific options

    # Optionally a pipeline of transformations, to be implemented by data deserializer's reader module:
    transforms = (
       [<transformationConfiguration1>]:
       [<transformationConfiguration2>]:
       ...
       [<transformationConfigurationN>]
    )
]
```
Transform configuration identifies the transform type and any transform-specific options:
```
[
    type = "<transformName>"
    # Transform-specific options
]
```
## Configuration Options 

### General Reader Configuration

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `verbosity`                           | Verbosity level (`0`, `1`, `2`), controls the diagnostic output of different components (Randomizer, Deserializer, Bundler, etc.) *Optional*, defaults to  `0`. |
| `randomize`                           | Specifies whether the input should be randomized ( `true`, `false`). Randomization method is identical to blockRandomize of the HTKMLFReader. *Optional*, defaults to  `true`. |
| `randomizationSeed`                   | Initial randomization seed value (incremented every sweep when the input data is re-randomized). *Optional*, defaults to  `0`. |
| `randomizationWindow`                 | Specifies the size (positive integer) of the randomization window (i.e., randomization range). This parameter affects how much of the dataset needs to reside in memory at once. *Optional*, defaults to the size of the **whole dataset** (in samples or in chunks, depending on the `sampleBasedRandomizationWindow` value). However, if one of the deserializers is `CNTKTextFormatDeserializer` and `sampleBasedRandomizationWindow` was **not** explicitly set to `true`, `randomizationWindow` will default to `128` (which is approximately 4GB of disk space worth of chunks). This parameter is ignored when `randomize` is `false`. |
| `sampleBasedRandomizationWindow`      | If `true`, the size of the randomization window is interpreted as a certain number of samples, and as a number of chunks otherwise. *Optional*, defaults to `true` if `CNTKTextFormatDeserializer` is not present in the list of deserializers, and to `false` otherwise.  Similarly to `randomizationWindow`, this parameter is ignored, when `randomize` is `false`. |
| `truncationLength`                    | Specifies the truncation length in samples for BPTT (positive integer). **Required** only if `truncated` is `true`, ignored otherwise. |
| `multiThreadedDeserialization`        | Specifies if multiple threads should be used when collecting sequences for a minibatch from the deserializers (`true`, `false`). *Optional*. |
| `frameMode`                           | Specifies if data should be randomized and returned at the frame or sequence level. When ``true``, input sequence are split into frames. *Optional*. Both `frameMode` and `truncated` can not be set to `true` at the same time. |
| `truncated`                           | When ``true``, enables truncated back-propagation through time (BPTT). *Optional*. Both `frameMode` and `truncated` can not be set to `true` at the same time. |
| `useNumericSequenceKeys`              | Sequence keys are used to correlated sequences between different deserializers. For some deserializers (i.e. HTK and MLF) the sequence keys are arbitrary strings. Storing them requires much memory on big corpus. If you are sure your sequence keys are numeric, please set this parameter to true, in that case all string keys will be converted to integers decreasing memory pressure. *Optional, default `false`.*|
| `hashSequenceKeys`                    | For the memory reasons described above, the string keys can also be hashed by setting this parameter to true. Please use it only for deserializers that support string sequence keys (HTK, MLF). *Optional, default `false`.*|
| `cacheIndex`                          | Specifies whether the meta-data built during the pre-processing stage should be written out to disk and loaded in from disk if available (`true`, `false`). *Optional*, defaults to `false`. For more details, see the section below. [!INCLUDE[versionadded-2.1](includes/versionadded-2.1.md)] |

##### Index caching

[!INCLUDE[index-caching-block](./includes/Index-Caching.md)]

`cacheIndex` has no effect on ImageDeserializer and CNTKBinaryFormatDeserializer, as the former does not index the input data and the later has the index information embedded in the format itself.

### General Deserializer Configuration

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `module`                              | Specifies the reader module name that implements the data deserializer. **Required**. |
| `type`                                | Specifies a data deserializer name exposed by the given reader module. **Required**.        |

### General Transform Configuration

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `type`                                | Specifies a transform name exposed by the reader module implementing the data deserializer. **Required**.  |

### HTKFeatureDeserializer options

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `scpFile`                             | A list of paths to SCP files to be processed. The files should be HTK compatible files and must be specified in the “archive” format.  The details of using an archive are described in [HTKMLF Reader](./BrainScript-HTKMLF-Reader.md).  **Required**. |
| `dim`                                 | An integer that specifies the full feature vector dimension with the desired context window.<sup>[1](#footnote1)</sup> **Required** |
| `contextWindow`                       | Can either be specified as a pair of positive integers or as a single positive integer (in which case it's interpreted as a pair with the same number repeated twice). Specifies left and right size (first and second integer of the pair) of the context window in samples. *Optional*, defaults to `1`. |
| `prefixPathInSCP`                     | A prefix string to apply to the paths specified within the SCP files. *Optional*. |

<a name = "footnote1"><sup>1</sup></a>
For example, if you had 72-dimensional features (24-dimensional filterbank features plus delta and delta-delta coefficients) and the network is designed to process a context window of 11 frames, the specified dimension should be 792.

### HTKMLFDeserializer options

| Parameter                             |             Description             |
|:--------------------------------------|-------------------------------------|
| `mlfFile`                             | Path to an HTK-style `mlf` file that contains the labels for all utterances specified in the `scp` file(s). **Required** if `mlfFileList` is not specified. |
| `mlfFileList`                         | Array of paths to HTK-style `mlf` file(s) that contains the labels for all utterances specified in the `scp` file(s).  **Required** if `mlfFile` is not specified. |
| `dim`                                 | Total cardinality of the label set (positive integer). **Required**. |
| `labelMappingFile`                    | Path to a file which lists all the labels seen in the `mlf` file, one per line. **Required**. |

labelDim can be used as a synonym for dim.

### CNTKTextFormatDeserializer options

Same options that can be used with [CNTKTextFormatReader](./BrainScript-CNTKTextFormat-Reader.md)

### ImageDeserializer options

* `file`: a simple text file where each line contains a tab-separated mapping between logical sequence key, image file (e.g. JPEG, PNG etc.) and 0-based label.

For more information please see [ImageReader](./BrainScript-Image-reader.md#crop-transform).

### Base64ImageDeserializer options

This deserializer supports the same options that can be used with ImageDeserializer. The only difference is in the format of the mapping file:

* `file`: a simple text file where each line contains a tab-separated mapping between logical sequence key (optional, can be omitted), 0-based category label and base 64 encoded image file (e.g. JPEG, PNG etc.).



## Examples of Configurations and Tests

You will find complete network definitions and the corresponding data set examples in the CNTK Repository.
There you will also find Unit and End-to-End Tests that use deserializers, i.e.
* https://github.com/Microsoft/CNTK/tree/release/latest/Tests/EndToEndTests/Speech/HTKDeserializers/LSTM/FullUtterance
* https://github.com/Microsoft/CNTK/tree/release/latest/Tests/EndToEndTests/Image/AlexNet
* https://github.com/Microsoft/CNTK/tree/release/latest/Tests/UnitTests/ReaderTests/Config/ImageAndTextReaderSimple_Config.cntk
* https://github.com/Microsoft/CNTK/tree/release/latest/Tests/UnitTests/ReaderTests/Config/CNTKTextFormatReader/dense.cntk
