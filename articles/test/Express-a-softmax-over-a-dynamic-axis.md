A softmax over a dynamic axis can be done via a recurrence:

BrainScript:
```
SoftMaxOverSequence (z) = {
    # define a recursive expression for \log(\sum_{i=1}^t \exp(z_i))
    runningLogSumExp = LogPlus (z,
                           PastValue (0, runningLogSumExp, defaultHiddenActivation=-1e30)) 
    logSumExp = If (BS.Loop.IsLast (runningLogSumExp),  # if last entry
                /*then*/ runningLogSumExp,              # then copy that
                /*else*/ FutureValue (0, logSumExp))    # else just propagate to the front
    result = Exp (z - logSumExp)
}.result
```