---
title:   BrainScript LU Sequence Reader
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# BrainScript LU Sequence Reader

Note: if you are a newcomer, please consider using [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader.md). In the future LUSequenceReader will be deprecated and eventually not supported.

LUSequenceReader is similar to LMSequenceReader. It however is used for language understanding tasks which have input and output strings that are different. An example of setting up LUSequenceReader is as follows

    reader = [
        readerType = "LUSequenceReader"
        randomize = "none"
        wordContext = 0:1:2
        nbrUttsInEachRecurrentIter = 10
        unk = "<unk>"
        wordMap = "$DataDir$\inputmap.txt"
        file = "$DataDir$\atis.train.IOB"
        labelIn = [
            useWordMap = true
            beginSequence = "BOS"
            endSequence = "EOS"
            token = "$DataDir$\input.txt"
        ]
        labels = [
            beginSequence = "O"
            endSequence = "O"
            token = "$DataDir$\output.txt"
        ]
    ]

The LUSequenceReader has following parameters:
* `wordContext`: this specifies a context window. For example, `wordContext=0:1:2` specifies a context window of 3. In this context window, it reads input at a current time, the next time and the time after the next time. Another example would be `wordContext=0:-1`. In such case, LUSequencReader reads a context window of 2 that consist of the current input and the immediate last input.

* `randomize`: it is either `None` or `Auto`. This specifies the mode of whether doing sentence randomization of the whole corpus.

* `nbrUttsInEachRecurrentIter`: this specifies the limit of the number of sentences in a minibatch. The reader arranges same-length input sentences, up to the specified limit, into each minibatch. For recurrent networks, trainer resets hidden layer activities only at the beginning of sentences. Activities of hidden layers are carried over to the next minibatch if an end of sentence is not reached. Using multiple sentences in a minibatch can speed up training processes.

* `unk`: this specifies the symbol to represent unseen input symbols. Usually, this symbol is “<unk>”.

* `wordMap`: this specifies a file that maps inputs to other inputs. This is useful if the user wants to map some inputs to unknown symbols. An example of the word mapping file is as follows:

    `buy buy`

    `trans <unk>`

* `file`: the file contains input and its labels. The last column is the label, and the other columns contain inputs. An example of training file is below. 

    `BOS O`

    `flight O`

    `from O`

    `charlotte B-fromloc.city_name`

    `to O`

    `las B-toloc.city_name`

    `vegas I-toloc.city_name`

    `EOS O`

In the above example you can also notice two sub-blocks named `labelIn` and `labels`.

* `labelIn`: the section for input label. It contains the following setups
  * `useWordMap` – `true` or `false`, specifies if using word map to map input words to other input words.
  * `beginSequence` – the sentence beginning symbol
  * `endSequence` – the sentence ending symbol
  * `token` – token file contains a list of input words. Their orders are not important.

* `labels`: the section for output label.
  * `token` – token file contains a list of output labels. Their order is not important as long as the tokens are unique.
