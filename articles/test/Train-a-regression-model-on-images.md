Below we describe how to predict one or more floating point value for an input image using CNTK. An example use case is to predict a bounding box, for example as (x, y, w, h), of an object in a given image. You could also think of predicting the price for a car just by looking at an image of that car (would be interesting actually). Here we use a very simple example in which we train a network to predict the average rgb values of an image (normalized to [0, 1]). However, the same steps apply for other use cases. These steps are:

1. Define both the image and the ground truth regression labels as inputs to your network
2. Define a network that predicts a matching number of values wrt your regression labels
3. Define a loss function that compares the predicted values with the ground truth
4. Adapt the reader section in your .cntk config file to read both image and regression labels

Here is how you could do it. The full config file is included in the Examples folder at [Examples/Image/Regression/RegrSimple_CIFAR10.cntk](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/Regression/RegrSimple_CIFAR10.cntk). That folder also contains the scripts to download the image data and generate the regression ground truth for training and testing.

1-3) Defining inputs, network and loss function:

```
    BrainScriptNetworkBuilder = [
        imageShape = 32:32:3
        featScale = Constant(1/256)
        labelDim = 3

        model (features) = {
            featNorm = Scale(features, featScale)
            h1 = LinearLayer {100,      init="gaussian", initValueScale=1.5} (featNorm)
            ol = LinearLayer {labelDim, init="gaussian", initValueScale=1.5} (h1)
        }.ol

        # inputs
        features = Input {imageShape}
        regrLabels = Input {labelDim}
        
        # apply model to features
        ol = model (features)

        # define regression loss
        # rmse = sqrt(SquareError(regrLabels, ol) / labelDim)
        sqerr = SquareError (regrLabels, ol)
        rmse = Sqrt (Constant(1/labelDim).* sqerr)

        featureNodes    = (features)
        labelNodes      = (regrLabels)
        criterionNodes  = (rmse)
        evaluationNodes = (rmse)
        OutputNodes     = (ol)
    ]
```

4) Defining a composite reader using both the ImageReader and CNTKTextFormatReader:

```
    reader = {
        verbosity = 0 ; randomize = true
        deserializers = ({
            type = "ImageDeserializer" ; module = "ImageReader"
            file = "$dataDir$/cifar-10-batches-py/train_map.txt"
            input = {
                features = { transforms = (
                    { type = "Scale" ; width = 32 ; height = 32 ; channels = 3 ; interpolations = "linear" } :
                    { type = "Transpose" }
                )}
                ignored = { labelDim = 10 }
            }
        } : {
            type = "CNTKTextFormatDeserializer" ; module = "CNTKTextFormatReader"
            file = "$dataDir$/cifar-10-batches-py/train_regrLabels.txt"
            input = {
                regrLabels = { dim = 3 ; format = "dense" }
            }
        })
    }
```

The reader is a composite reader that uses the ImageReader to read images and the CNTKTextFormatReader to read the regression ground truth labels. It does so by defining an array of deserializers (using `{...} : {...}`) and assigning the inputs as defined in the network above (cf. features and regrLabels).

See [Examples/Image/Miscellaneous/CIFAR-10/06_RegressionSimple.cntk](https://github.com/Microsoft/CNTK/blob/master/Examples/Image/Miscellaneous/CIFAR-10/06_RegressionSimple.cntk) for the full config file and the corresponding Readme in that folder for running the example.
