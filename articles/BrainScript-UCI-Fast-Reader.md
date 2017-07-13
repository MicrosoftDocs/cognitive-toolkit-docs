---
title:   BrainScript UCI Fast Reader
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript UCI Fast Reader

> [!WARNING]
> UCIFastReader is **deprecated** and is being replaced by [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader.md). Please use [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader.md) for your networks.

----------

UCIFastReader reads text-based UCI format data, in which each data record is a line of space-delimited floating point feature and label values. The label information is either at the beginning or the end of each line, if label information is provided. To use the UCIFastReader you set the `readerType` to `UCIFastReader` as in

    reader = [
        readerType = "UCIFastReader"
        file = "c:\cntk\data\mnist\mnist_train.txt"
        features = [
            dim = 784
            start = 1
        ]
        labels = [
            dim = 1
            start = 0
            labelDim = 10
            labelMappingFile = "c:\cntk\data\mnist\mnistlabels.txt"
        ]
    ]

In this example you can also notice two sub-blocks named `features` and `labels`. These names are used by the data readers to match the computation node in your network and the data loaded from the files. If simple network builders are used to create your network, `features` and `labels` are the standard names of the feature and label nodes, respectively. If you defined your network using the NDL network builder you need to make sure these names matches the corresponding nodes in your network. The UCIFastReader has following parameters:
* `file`: the file that contains the dataset. This parameter has been moved up from the features and labels sub-blocks, because UCIFastReader requires the file be the same, and moving up a level makes sure this restriction is met.

* `dim`: the dimension of the input value. Note that each column in the UCI data file represents one dimension of the input data.

* `start`: the start column (zero-based) of the input data.

* `labelDim`: the number of possible label values. This parameter is required for categorical labels since the dimension of the label node will be determined by this value. Note that the label value itself is typically specified in one column in the UCI data file.

* `labelMappingFile`: the path to a file used to map from the label value to a numerical label identifier. The file typically lists all possible label values, one per line, which might be text or numeric. The zero-based line number is the identifier that will be used by CNTK to identify that label. It’s important that the same label mapping file is used for training and evaluation. This can be done by moving the labelMappingFile parameter up so that it can be shared by both the training and evaluation blocks.
* `customDelimiter`: the customized delimiter. By default, spaces are used as the delimiter. With this parameter you can choose to use another delimiter such as comma or semicolon in addition to spaces. Note though, this reader does not handle empty fields, e.g., two commas in a row, with or without whitespace in-between.
* `customDecimalPoint`: the customized decimal point. By default, dot is used as the decimal point. With this parameter you can choose to use another decimal point such as those used in European countries. 
* `labelType`: to indicate how the label columns should be interpreted. By default it is set to "Category" which means it requires a mapping table to map from values (can be string) to class IDs so that values are converted to 1-hot representation. It can be set to "Regression" to indicate that the values are used directly without any conversion or "None" to indicate no labels are used.

Other options:
* `traceLevel`: An integer that defines the verbosity of the reader. Currently used: 0 (no tracing) and 1. Defaults to 0.
* `prefetch`: A boolean ("true" or "false") indicating whether prefetching should be enabled. Prefetching refers to the fact that data is read asynchronously to the main processing. Defaults to false.
* `randomize`: A string ("none", "auto") controlling the order in which lines should be read. In case of "None" they are read in linear order; In case of "auto" they are picked randomly; TODO can also be an integer under certain constraints.
* `minibatchMode`: If given and set to "partial", partial minibatches are accepted; Otherwise the training data size must be a multiple of the minibatch Size.
