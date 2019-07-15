---
title:   BrainScript epochSize in CNTK 
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/10/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# BrainScript epochSize in CNTK 

For Python users, see [here](./Interpreting-epoch_size-and-minibatch_size_in_samples-and-MinibatchSource.next_minibatch-in-CNTK.md).  

The number of **label** samples (tensors along a dynamic axis) in each epoch. The `epochSize` in CNTK is the number of **label** samples after which specific additional actions are taken, including
* saving a checkpoint model (training can be restarted from here)
* cross-validation
* learning-rate control
* minibatch-scaling

Note that the definition of the number of label samples is similar to the number of samples used for [minibatchSize (minibatch_size_in_samples)](./BrainScript-minibatchSize-and-Python-minibatch_size_in_samples-in-CNTK.md). The definition of `epochSize` differs from the definition of `minitbatchSize` in the sense that `epochSize` is **label** samples, not input samples.

So, importantly, for sequential data, a sample is an individual item of a sequence.
Hence, CNTK's `epochSize` does *not* refer to a number of *sequences*,
but the number of sequence *items* across the sequence **labels** that constitute the minibatch.  

Equally important, it is **label** samples, not input samples, and the number of labels per sequence is not necessarily the number of input samples.  It is possible, for example, to have one label per sequence and for each sequence to have many samples (in which case `epochSize` acts like number of sequences), and it is possible to have one label per sample in a sequence, in which case `epochSize` acts exactly like `minibatchSize` in that every sample (not sequence) is counted.

For smaller dataset sizes, `epochSize` is often set equal to the dataset size. In BrainScript you can specify 0 to denote that. In Python you can specify `cntk.io.INFINITELY_REPEAT` for that. In Python only, you can also set it to `cntk.io.FULL_DATA_SWEEP` where processing will stop after one pass of the whole data size.

For large datasets, you may want to guide your choice for epochSize by checkpointing. For example, if you want to lose at most 30 minutes of computation in case of a power outage or network glitch, you would want a checkpoint to be created about every 30 minutes (from which the training can be resumed). Choose `epochSize` to be the number of samples that takes about 30 minutes to compute.
