Use the composite reader to specifiy the two files, one for lables, and one for features.  And make sure to match sequence id's in labels file and the features file.

```
reader = [
  …
  deserializers = (
  [
      type = "CNTKTextFormatDeserializer" ; module = "CNTKTextFormatReader"
      file = "$RootDir$/features.txt"
      input = [ features = [...]]
  ]:[
      type = "CNTKTextFormatDeserializer" ; module = "CNTKTextFormatReader"
      file = "$RootDir$/labels.txt"
      input = [ labels = [...]]
  ]
]
```

