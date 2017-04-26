---
title:   How do I evaluate models in BrainScript
author:    chrisbasoglu
date:    04/12/2017
ms.author:   cbasoglu
ms.date:   04/12/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   brainscript
---

# How do I evaluate models in BrainScript

* [Do early stopping](#do-early-stopping)? 
* [Monitor the error on a held out set during training](#monitor-the-error-on-a-held-out-set-during-training)?  
* [Set the dropout rate to 0 during evaluation/testing](#Dropout-during-evaluation)? 
* [Evaluate my newly trained model but output the activations at an intermediate layer](#evaluate-my-newly-trained-model-but-output-the-activations-at-an-intermediate-layer)? 
* [Associate an id with a prediction](#associate-an-id-with-a-prediction)? 
* [Deploy model evaluation on Windows](#eval-samples-in-cntk-binary-download-package-for-windows)? 
* [Evaluate a model in an Azure WebApi](./Evaluate-a-model-in-an-Azure-WebApi.md) 

## Do early stopping

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

## Monitor the error on a held out set during training

To monitor the error on a held out set during training specify a "cvReader" section inside your training block. This typically looks the same as the reader section but points to the held out data.

For example:
```
trainDNN = {
  action = "train"
  ...
  reader = {
    readerType = "CNTKTextFormatReader"
    file = "train.txt"
    input = {
        features = { dim = 100000 ; format = "sparse" }
        labels =   { dim = 10; format = "sparse" }
    }
  }

  cvReader = {
    readerType = "CNTKTextFormatReader"
    file = "validation.txt"
    input = {
        features = { dim = 100000 ; format = "sparse" }
        labels =   { dim = 10; format = "sparse" }
    }
  }
  ...
}
```

## Dropout during evaluation

During evaluation, the dropout rate is automatically set 0, so you don't have to do anything.

## Evaluate my newly trained model but output the activations at an intermediate layer

To evaluate an output of a model that was not originally declared as a output,
create a "new" network in the write or test action instead of specifying a `modelPath`,
which is constructed by loading an old model and defining a new set of output nodes:

    myWriteAction =
    {
        action = "write"
        BrainScriptNetworkBuilder = {
            network = BS.Network.Load ("$modelPath$")
            outputNodes = (network.yourIntermediateNode)
        }
        ...
    }

where `yourIntermediateNode` denotes the node you want to change into an output.
Sometimes, node names are not identical to the original variable name
used to declare them in BrainScript.
In this case, consult the validation output in the log and use the full name shown there.

If the name of `yourIntermediateNode` contains periods (`.`) or brackets (`[ ]`), please replace them with underscores (`_`). If your network contained dynamic axes, you might have to redefine them.

## Associate an id with a prediction

Assuming you have an input called Id (which contains a unique numeric id for each example) in your file you can do
```
predictionWithId = Splice(prediction:Id)
```  

## Deploy model evaluation on Windows

Deploying a trained model from CNTK on Windows is discussed [here](./CNTK-Evaluation-Overview#eval-samples-in-cntk-binary-download-package-for-windows)

## Evaluate a model in an Azure WebApi

Evaluating a trained model from CNTK in Azure via Web Api is discussed [here](./Evaluate-a-model-in-an-Azure-WebApi) 


