---
title:   Using CNTK with C#
author:    liqfu
ms.author:   liqfu
ms.date:  08/30/2017
ms.custom:   cognitive-toolkit
ms.topic:   
ms.service:  
ms.devlang:   csharp
---

# C#/.NET Managed API

CNTK v2.2.0 provides C# API to build, train, and evaluate CNTK models. This section gives an overview of CNTK C# API. [C# training examples](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCSharp) are available in CNTK github repository.

## Using C#/.NET Managed API to Build a Deep Neural Network
CNTK C# API provides basic operations in CNTKLib namespace. A CNTK operation takes one or two input variables with necessary parameters and produces a CNTK Function. A CNTK Function maps input data to output. A CNTK Function may also be treated as a varaible and be taken as input to another CNTK operation. With this mechanism, a Deep Neural Network can be built with basic CNTK operations by chaining and composition. As an example:
***
```cs
private static Function CreateLogisticModel(Variable input, int numOutputClasses)
{             
    Parameter bias = new Parameter(new int[]{numOutputClasses}, DataType.Float, 0}
    Parameter weights = new Parameter(new int[]{input.Shape[0], numOutputClasses}, DataType.Float,        
      CNTKLib.GlorotUniformInitializer( 
        CNTKLib.DefaultParamInitScale, 
        CNTKLib.SentinelValueForInferParamInitRank, 
        CNTKLib.SentinelValueForInferParamInitRank, 1));
    var z = CNTKLib.Plus(bias, CNTKLib.Times(weights, input));    
    Function logisticClassifier = CNTKLib.Sigmoid(z, "LogisticClassifier");
    return logisticClassifier;
}
```  
CNTKLib.Plus, CNTKLib.Times, CNTKLib.Sigmoid are basic CNTK operations. The input argument can be a CNTK Variable representing data features. It may also be another CNTK function. This code builds a simple computation network with parameters that are adjusted at training stage to make a decent multi-class classifier. 

CNTK C# API provides opteraions to build convolution neural networks (CNN) and recurrent neural networks (RNN). For example, to build a 2-layer CNN image classifier:
***
```cs
    var convParams1 = new Parameter(
      new int[] { kernelWidth1, kernelHeight1, numInputChannels, outFeatureMapCount1 }, 
      DataType.Float, CNTKLib.GlorotUniformInitializer(convWScale, -1, 2), device);
    var convFunction1 = CNTKLib.ReLU(CNTKLib.Convolution(
      convParams1, input, 
      new int[] { 1, 1, numInputChannels } ));
    var pooling1 = CNTKLib.Pooling(convFunction1, PoolingType.Max,
        new int[] { poolingWindowWidth1, poolingWindowHeight1 }, new int[] { hStride1, vStride1 }, new bool[] { true });
    
    var convParams2 = new Parameter(
      new int[] { kernelWidth2, kernelHeight2, outFeatureMapCount1, outFeatureMapCount2 }, 
      DataType.Float, CNTKLib.GlorotUniformInitializer(convWScale, -1, 2), device);
    var convFunction2 = CNTKLib.ReLU(CNTKLib.Convolution(
      convParams2, pooling1, 
      new int[] { 1, 1, outFeatureMapCount1 } ));
    var pooling2 = CNTKLib.Pooling(convFunction2, PoolingType.Max,
        new int[] { poolingWindowWidth2, poolingWindowHeight2 }, new int[] { hStride2, vStride2 }, new bool[] { true });

    var imageClassifier = TestHelper.Dense(pooling2, numClasses, device, Activation.None,   "ImageClassifier");        
```

An example to build a RNN with long-short-term-memory ([LTSM](https://github.com/Microsoft/CNTK/blob/master/Examples/TrainingCSharp/Common/LSTMSequenceClassifier.cs)) is also provided.
    
## Data Preparation Using C#/.NET
CNTK provides data preparation utilities for training. CNTK C# API exposes these utilities. It takes data from various preprocessed forms. Data loading and batching are done efficiently. For example, assuming we have data in text in the following CNTK text format called "Train.ctf":
```
|features 3.854499 4.163941 |labels 1.000000
|features 1.058121 1.204858 |labels 0.000000
|features 1.870621 1.284107 |labels 0.000000
|features 1.134650 1.651822 |labels 0.000000
|features 5.420541 4.557660 |labels 1.000000
|features 6.042731 3.375708 |labels 1.000000
|features 5.667109 2.811728 |labels 1.000000
|features 0.232070 1.814821 |labels 0.000000
```

A CNTK DataSource is create in this way:
***
```cs
    var minibatchSource = MinibatchSource.TextFormatMinibatchSource(
        Path.Combine(DataFolder, "Train.ctf"), streamConfigurations,
        MinibatchSource.InfinitelyRepeat, true);
```
Later batch data can be retrieved and used for training:
***
```cs
    var minibatchData = minibatchSource.GetNextMinibatch(minibatchSize, device);
```

## Using C#/.NET Managed API to Train a Deep Neural Network
Stochastic gradient descent (SGD) is a way to optimize model parameters with minibatch training data. CNTK supports many SGD variations that are commonly seen in deep learning literature. They are exposed by CNTK C# API:  

```
SGDLearner - the CNTK built-in SGD learner
MomentumSGDLearner - the CNTK built-in Momentum SGD learner
FSAdaGradLearner - FSAdaGrad learner as the original [paper](http://mirlab.org/conference_papers/International_Conference/ICASSP%202014/papers/p235-seide.pdf)
AdamLearner - Adam learner as the original [paper](https://arxiv.org/pdf/1412.6980.pdf)
AdaGradLearner - the CNTK built-in [AdaGrad learner](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2010/EECS-2010-24.pdf)
RMSPropLearner - the CNTK built-in RMSProp learner
AdaDeltaLearner - the CNTK built-in [AdaDelta learner](https://arxiv.org/abs/1212.5701)
For a general overview of different learning optimizers, see [Stochastic gradient descent] (https://en.wikipedia.org/wiki/Stochastic_gradient_descent).
```

A CNTK trainer is used to do minibatch training. [A C# code snip](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCSharp/Common/LogisticRegression.cs) for minibatch training:
***
```cs
    // build a learning model
    var featureVariable = Variable.InputVariable(new int[] { inputDim }, DataType.Float);
    var labelVariable = Variable.InputVariable(new int[] { numOutputClasses }, DataType.Float);
    var classifierOutput = CreateLinearModel(featureVariable, numOutputClasses, device);
    var loss = CNTKLib.CrossEntropyWithSoftmax(classifierOutput, labelVariable);
    var evalError = CNTKLib.ClassificationError(classifierOutput, labelVariable);

    // prepare for training
    var learningRatePerSample = new CNTK.TrainingParameterScheduleDouble(0.02, 1);
    var parameterLearners =
        new List<Learner>() { Learner.SGDLearner(classifierOutput.Parameters(), learningRatePerSample) };
    var trainer = Trainer.CreateTrainer(classifierOutput, loss, evalError, parameterLearners);

    int minibatchSize = 64;
    int numMinibatchesToTrain = 1000;

    // train the model
    for (int minibatchCount = 0; minibatchCount < numMinibatchesToTrain; minibatchCount++)
    {
        Value features, labels;
        GenerateValueData(minibatchSize, inputDim, numOutputClasses, out features, out labels, device);
        trainer.TrainMinibatch(
            new Dictionary<Variable, Value>() { { featureVariable, features }, { labelVariable, labels } }, device);
        TestHelper.PrintTrainingProgress(trainer, minibatchCount, 50);
    }
```
  
In this code snip, a CNTK built-in SGD learner with per sample learning rate = 0.02 is used. The learner is to optimize all parameters of the model. A trainer is created with the learner, a loss function, and a evaluation function. During each training iteration minibatch data is fed to the trainer to have model parameters updated. Trainig loss and evaluation error are displayed with a helper method during the training.  
Here we generate 2 classes of statistically separable data of labels and features.
In other more realistic [examples](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCSharp/Common), public test data are loaded with CNTK MinibatchSource as discussed above.   


## Using C#/.NET Managed API to Evaluate a Deep Neural Network

C# API has evaluation API to do model evaluation. Most [C# training examples](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCSharp) do model evaluation after training.   


More model evaluation details using CNTK C# API can be found at
* [CNTK-library evaluation on Windows](./CNTK-Library-Evaluation-on-Windows.md)
* [Evaluation in Azure](./Evaluate-a-model-in-an-Azure-WebApi.md)
* [Evaluation on Universal Windows Platform (UWP)](./CNTK-Library-Evaluation-on-UWP.md)
* [NuGet-Packages](./NuGet-Package.md)

## Get started with examples
Once you have gone through this overview, you may proceed with C# training examples in two ways: to work with CNTK github source or to work with CNTK examples using [CNTK Nuget for Windows](NuGet-Package.md).   
### work with CNTK source
- Following [Setp CNTK on Windows](Setup-CNTK-on-Windows.md) to setup CNTK on windows.
- Build CNTK.sln with Visual Studio. 
- [Prepare sample data](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCShape).
- Run examples as EndToEnd test in CNTKLibraryCSTrainingTest.csproj

### work with CNTK examples with CNTK NuGet
- Download CNTK C# Training [examples](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCSharp)
- [Prepare sample data](https://github.com/Microsoft/CNTK/tree/master/Examples/TrainingCShape).
- Build and run the examples.

