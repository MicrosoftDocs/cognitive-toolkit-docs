---
title:   Interpreting epoch_size, minibatch_size_in_samples and MinibatchSource.next_minibatch in CNTK
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  12/04/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:  python
---

# Interpreting epoch_size, minibatch_size_in_samples and MinibatchSource.next_minibatch in CNTK
In this article, we will clarify the interpretation and usage of the following parameters and functions in Python:
* [epoch_size](#epoch_size)
* [minibatch_size_in_samples](#minibatch_size_in_samples)
* [MinibatchSource.next_minibatch](#minibatchSourcenext_minibatch)

## epoch_size

The number of **label** samples (tensors along a dynamic axis) in each epoch. The `epoch_size` in CNTK is the number of **label** samples after which specific additional actions are taken, including
* saving a checkpoint model (training can be restarted from here)
* cross-validation
* learning-rate control
* minibatch-scaling

Note that the definition of the number of label samples is similar to the number of samples used for [minibatch_size_in_samples](./BrainScript-minibatchSize-and-Python-minibatch_size_in_samples-in-CNTK.md). The definition of `epoch_size` differs from the definition of `minibatch_size_in_samples` in the sense that `epoch_size` is **label** samples, not input samples.

So, importantly, for sequential data, a sample is an individual item of a sequence.
Hence, CNTK's `epoch_size` does *not* refer to a number of *sequences*,
but the number of sequence *items* across the sequence **labels** that constitute the minibatch.  

Equally important, `epoch_size` refers to **label** samples, not input samples, and the number of labels per sequence is not necessarily the number of input samples.  It is possible, for example, to have one label per sequence and for each sequence to have many samples (in which case `epoch_size` acts like number of sequences), and it is possible to have one label per sample in a sequence, in which case `epoch_size` acts exactly like `minibatch_size_in_samples` in that every sample (not sequence) is counted.

For smaller dataset sizes, `epoch_size` is often set equal to the dataset size. In Python you can specify `cntk.io.INFINITELY_REPEAT` for that. In Python only, you can also set it to `cntk.io.FULL_DATA_SWEEP`, wherein processing will stop after one pass of the whole data size.

For large data sets, you may want to guide your choice for `epoch_size` by checkpointing. For example, if you want to lose at most 30 minutes of computation in case of a power outage or network glitch, you would want a checkpoint to be created about every 30 minutes (from which the training can be resumed). Choose `epoch_size` to be the number of samples that takes about 30 minutes to compute.

## minibatch_size_in_samples

Note: For BrainScript users, the parameter for minibatch size is `minibatchSize`; for Python users, it is `minibatch_size_in_samples`.

CNTK has a very specific definition of the `minibatch_size_in_samples` parameter: It denotes **the number of samples between model updates**.
A *sample* here is defined as one vector or tensor flowing through the system.
For example, in an image recognition task, one image is one sample.

The minibatch size for each epoch is given in *samples* (tensors along a dynamic axis). The default value is `256`. You can use different values for different epochs; e.g., `128*2 + 1024` (in Python) means using a minibatch size of 128 for the first two epochs and then 1024 for the rest.
Note that 'minibatch size' in CNTK means the number of samples processed between model updates. This definition also holds when parallelizing across workers (e.g. for `K` workers, 
the number of samples each worker would process is `minibatch_size_in_samples/K`).
In case of variable-length inputs, `minibatch_size_in_samples` refers to the number of items in these sequences,
*not* the number of sequences.
SGD will try to fit up to as many sequences as possible into the minibatch that do not exceed `minibatch_size_in_samples` total samples.
If several inputs are given, tensors are added to the current minibatch until one of the inputs exceeds the `minibatch_size_in_samples`.


Importantly, for sequential data, a sample is an individual item of a sequence.
Hence, CNTK's `minibatch_size_in_samples` does *not* refer to the
number of *sequences* in the minibatch,
but the *aggregate number of sequence items/tokens* across the sequences that constitute the minibatch.
CNTK has native support for variable-length sequences, i.e. it can accommodate
sequences of highly varying lengths within the same minibatch, without need for workarounds like bucketing.
Together with CNTK's notion of specifying the learning rate per sample (instead of a minibatch average),
every item of sequences of any length contributes the same to the gradient,
leading to consistent convergence.
(Many other toolkits define the minibatch size for sequential data as the number of *sequences*
in the minibatch.
This is problematic, especially if gradients are also defined as minibatch averages rather than
CNTK's minibatch sums, because the contribution to the gradient from each token or step in a sequence
would be inversely proportional to the sequence length. CNTK's approach avoids this.)

When multiple inputs are used, it is possible that not all inputs have the same sequence length.
For example, in sequence classification the label of each sequence is a single token.
In this case, the input with the largest number of samples controls the minibatch size. (You can change this behavior by specifying `defines_mb_size=True` for some input, then the minibatch size will be counted based on the sequences from this particular input. When several inputs are specified, only a single one can have `defines_mb_size` set to `True`.)

Despite our clear definition of `minibatch_size_in_samples` being the number of samples between model updates,
there are two occasions where we must relax the definition:
* sequential data: Variable-length sequences do not generally sum up to exactly the requested
minibatch size. In this case, as many sequences as possible are packed into a minibatch without exceeding the requested
minibatch size (with one exception: If the next one sequence in the randomized corpus exceeds the
length of the minibatch size, the minibatch size will consist of this sequence).
* data parallelism: Here, the minibatch size is approximate, as our chunk-based randomization algorithm cannot guarantee
that each worker receives precisely the same number of samples.

All of the above considerations also apply to `epoch_size`, but `epoch_size` has some differences, see [above](#epoch_size).

## MinibatchSource.next_minibatch

The method [MinibatchSource.next_minibatch()](https://github.com/MicrosoftDocs/cognitive-toolkit-docs/edit/mxiao/restruct/articles) reads a minibatch that contains data for all input streams. When called during training, `MinibatchSource.next_minibatch(minibatch_size_in_samples, input_map)` will pick a random subset of `k` samples from the training dataset, where `k=minibatch_size_in_samples`.  

The implementation ensures that when `next_minibatch` is called `N` times (where `N = number_of_training_samples/minibatch_size_in_samples`), the entire training dataset gets covered at the end of the `N` calls of `next_minibatch`.
This also means that when `next_minibatch` is called `2*N` times, the entire dataset gets covered twice.

**Additional information:**
* Each cycle through the data will have a different random order.
* If you double your minibatch size, one minibatch will now contain exactly the samples that, before, the corresponding two consecutive minibatches would have contained (this may be approximate if you have variable-length sequences). I.e. two runs that only differ in minibatch size process the data in the same order.
* If you interrupt and restart from checkpoint, you will get the same random order as if you had not interrupted training. This is implemented by grounding the reading/randomization process on a nominal time axis, with this simple algorithm:
  * Training proceeds on a nominal infinite time axis. If you fetch a minibatch of size 256, the nominal time progresses by 256.
  * The training corpus is replicated an infinite number of times on this time axis. If you have `M` samples, then the first replica spans nominal time `0..M-1`; the second `M..2M-1`, etc.
  * Each replica is random-shuffled within, but not across, replica boundaries. I.e. once you have processed precisely `M` samples, you have seen each sample exactly once.
  * Calling `next_minibatch(K)` gives you the next `K` samples on this reshuffled infinite timeline. It’s the same as calling `next_minibatch(1)` for `K` times.
  * This is all done lazily.
  * Restarting from checkpoint is as simple as resetting the nominal time to the nominal time when the checkpoint was created.



