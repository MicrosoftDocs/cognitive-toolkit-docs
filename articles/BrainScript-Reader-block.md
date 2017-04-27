---
title:   BrainScript Reader Block
author:    chrisbasoglu
date:    03/15/2017
ms.author:   cbasoglu
ms.date:   03/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript Reader Block 

The reader block is used for all types of readers and the `readerType`  parameter determines which reader to use. Each reader implements the same IDataReader interface. Many parameters in the reader block are shared across different types of readers. Some are specific to a particular reader. A simple reader block using the CNTKTextFormatReader could look like this:

    reader = [
        readerType = "CNTKTextFormatReader"
        file = "$DataDir$/Train-28x28_cntk_text.txt"
        input = [
            features = [
                dim = 784
                format = "dense"
            ]
            labels = [
                dim = 10
                format = "dense"
            ]
        ]
    ]

You can explore different reader settings in the configurations of the [Examples](./Examples.md). For details regarding specific readers see the corresponding wiki pages:

* [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader.md)
* [UCI Fast Reader (deprecated)](./BrainScript-UCI-Fast-Reader.md)
* [HTKMLF Reader](./BrainScript-HTKMLF-Reader.md)
* [LM Sequence Reader](./BrainScript-LM-Sequence-Reader.md)
* [LU Sequence Reader](./BrainScript-LU-Sequence-Reader.md)
