---
title:   BrainScript LM Sequence Reader
author:    chrisbasoglu
date:    03/15/2017
ms.author:   cbasoglu
ms.date:   03/15/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# BrainScript LM Sequence Reader

Note: if you are a newcomer, please consider using [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader.md). In the future LMSequenceReader will be deprecated and eventually not supported.

LMSequenceReader is a reader that reads text string. It is mostly often used for language modeling tasks. An example of its setup is as follows

    reader = [
        readerType = "LMSequenceReader"
        randomize = false
        nbruttineachrecurrentiter = 10
        unk = "<unk>"
        wordclass = "$DataDir$\wordclass.txt"
        file = "$DataDir$\penntreebank.train.txt"
        labelIn = [
            labelDim = 10000
            beginSequence = "</s>"
            endSequence = "</s>"
        ]
    ]

The LMSequenceReader has following parameters:
* `randomize`: it is either `None` or `Auto`. This specifies the mode of whether doing sentence randomization of the whole corpus.

* `nbruttsineachrecurrentiter`: this specifies the limit of the number of sentences in a minibatch. The reader arranges same-length input sentences, up to the specified limit, into each minibatch. For recurrent networks, trainer resets hidden layer activities only at the beginning of sentences. Activities of hidden layers are carried over to the next minibatch if an end of sentence is not reached. Using multiple sentences in a minibatch can speed up training processes.

* `unk`: this specifies the symbol to represent unseen input symbols. Usually, this symbol is “<unk>”. Unseen words will be mapped to the symbol.

* `wordclass`: this specifies the word class information. This is used for class based language modeling. An example of the class information is below. The first column is the word index. The second column is the number of occurrences, the third column is the word, and the last column is the class id of the word.

    `0 42068 </s> 0`

    `1 50770 the 0`

    `2 45020 <unk> 0`

    `3 32481 N 0`

    `4 24400 of 0`

    `5 23638 to 0`

    `6 21196 a 0`

    `7 18000 in 1`

    `8 17474 and 1`

* `file`: the file contains text strings. An example is below. In this example you can also notice one sub-blocks named `labelIn`.

    </s> pierre <unk> N years old will join the board as a nonexecutive director nov. N </s>
    </s> mr. <unk> is chairman of <unk> n.v. the dutch publishing group </s>

* `labelIn`: the section for input label. It contains the following setups
  * `beginSequence` – the sentence beginning symbol
  * `endSequence` – the sentence ending symbol
  * `labelDim` – the dimension of labels. This usually means the vocabulary size.
