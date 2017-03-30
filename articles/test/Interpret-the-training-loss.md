How does CNTK calculate the training loss (as bolded) after an epoch is finished? Does the final model go through the whole training set to get the value, or does it simply average the miniBatch training losses produced by the model while it gets  updated during the epoch?   
<pre><code>Finished Epoch[ 1 of 100]: [Training] <b>CE = 0.15920662 * 59999999</b>; totalSamplesSeen = 59999999; learningRatePerSample = 9.9999997e-05; epochTime=641.392s
Final Results: Minibatch[1-3510]: CE = 0.32190538 * 3592929
Finished Epoch[ 1 of 100]: [Validate] CE = 0.32190538 * 3592929</pre></code>

The answer is the latter (where the averaging is weighted by the samples in the minibatch in case they are variable-length). I.e. you do not get the actual training loss of the model in the state it is in at the end of the epoch.