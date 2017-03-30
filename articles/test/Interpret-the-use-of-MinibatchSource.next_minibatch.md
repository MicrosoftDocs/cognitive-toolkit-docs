**TODO** This material applies to all of CNTK and should move to a common location

My current understanding is that when I call MinibatchSource.next_minibatch(minibatch_size, input_map) during training it will pick a random subset of minibatch_size samples from my training data set?

**Yes**

Does the implementation ensure that when I call next_minibatch N times (N = number_of_training_samples / minibatch_size) my whole data set gets covered at the end of the N calls of next_minibatch? Also, when I call next_minibatch 2*N times does it means that my whole training set gets covered twice?

**Yes**.

**Additional info:**
* Each cycle through the data will have a different random order.
* If you double your minibatch size, one minibatch will now contain exactly the samples that, before, the corresponding two consecutive minibatches would have contained (this may be approximate if you have variable-length sequences). I.e. two runs that only differ in MB size process the data in the same order.
* If you interrupt and restart from checkpoint, you will get the same random order as if you had not interrupted.

This is implemented by grounding the reading/randomization process on a nominal time axis, with this simple algorithm:

* Training proceeds on a nominal infinite time axis. If you fetch a MB of size 256, the nominal time progresses by 256.
* The training corpus is replicated an infinite number of times on this time axis. If you have M samples, then the first replica spans nominal time 0..M-1; the second M..2M-1; etc.
* Each replica is random-shuffled within, but not across replica boundaries. I.e. once you have processed precisely M samples, you have seen each sample exactly once.
* Calling next_minibatch(K) gives you the next K samples on this reshuffled infinite time line. It’s the same as calling next_minibatch(1) for K times.
* This is all done lazily.
* Restarting from checkpoint is as simple as resetting the nominal time to the nominal time when the checkpoint was created.
