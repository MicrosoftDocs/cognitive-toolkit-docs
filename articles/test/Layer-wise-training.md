To perform layer-wise training simply use multiple "commands" in your config file, where each command is of type action=train.
```
command = TrainLayer1:TrainLayer2:TrainLayer3:EndToEndTrain:Test:Output

TrainLayer1= [ 
   action = train
   ...
]

TrainLayer2= [ 
   action = train
   ...
]
...
```