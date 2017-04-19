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