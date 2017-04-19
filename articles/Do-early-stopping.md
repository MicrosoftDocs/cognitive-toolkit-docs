To do early stopping you have to train until the end and then pick the checkpoint that is performing the best on a validation set. To do this include the cv action in your config
```
command=TrainModel:EvaluateCheckpoints
...
EvaluateCheckpoints = {
    action = "cv"
    reader = {
         file = $myValidationSet$
         #rest of the options same as the "test" reader
         ...
    }
    crossValidationInterval = 3:2:9 # evaluate epochs 3 to 9 with a step of 2 i.e. 3,5,7,9
    sleepTimeBetweenRuns = 0 # let the GPU cool off for this many seconds
    #rest of the options same as the "test" action
    ...
}
```
The cv command will print the best model on the validation set. Then you can evaluate that model in your test action on the final test set. 
