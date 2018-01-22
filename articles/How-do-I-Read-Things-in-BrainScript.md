---
title:   How do I read things in BrainScript
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/12/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# How do I read things in BrainScript

* [Specify multiple label streams with the HTKMLFReader](#specify-multiple-label-streams-with-the-htkmlfreader)? 
* [Use the built-in readers to train a network model using multiple input files](#use-built-in-readers-with-multiple-inputs)? 
* [Put labels and features in separate files with CNTKTextFormatReader](#put-labels-and-features-in-separate-files-with-cntktextformatreader)?

## Specify multiple label streams with the HTKMLFReader

The HTKMLFReader (the reader to read Master Label Files (MLF) of the Hidden Markov Toolkit (HTK))
can be configured to read multiple label streams. The example below is taken from 
[TIMIT_TrainMultiTask_ndl_deprecated.cntk](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Speech/Miscellaneous/TIMIT/config/TIMIT_TrainMultiTask_ndl_deprecated.cntk)
in the Examples directory:

    reader = {
        readerType = "HTKMLFReader"
        ...
        labels = {
            mlfFile = "$MlfDir$/TIMIT.train.align_cistate.mlf.cntk"
            labelMappingFile = "$MlfDir$/TIMIT.statelist"
            labelDim = 183
            labelType = "category"
        }
        regions = {
            mlfFile = "$MlfDir$/TIMIT.train.align_dr.mlf.cntk"
            labelDim = 8
            labelType = "category"
        }
    }

## Use built in readers with multiple inputs

See the description at [Understanding and Extending Readers](./BrainScript-and-Python---Understanding-and-Extending-Readers.md) and look for the section describing how to "compose several data deserializers" 

## Put labels and features in separate files with CNTKTextFormatReader

Use the composite reader to specify the two files, one for labels, and one for features.  And make sure to match sequence id's in labels file and the features file.

```
reader = [
  …
  deserializers = (
  [
      type = "CNTKTextFormatDeserializer" ; module = "CNTKTextFormatReader"
      file = "$RootDir$/features.txt"
      input = [ features = [...]]
  ]:[
      type = "CNTKTextFormatDeserializer" ; module = "CNTKTextFormatReader"
      file = "$RootDir$/labels.txt"
      input = [ labels = [...]]
  ]
]
```

