The reader block is used for all types of readers and the `readerType`  parameter determines which reader to use. Each reader implements the same IDataReader interface. Many parameters in the reader block are shared across different types of readers. Some are specific to a particular reader. A simple reader block using the CNTKTextFormatReader could look like this:

    reader = [
        readerType = "CNTKTextFormatReader"
        file = "$DataDir$/Train-28x28_cntk_text.txt"
        input = [
            features = [
                dim = 784
                format = "dense"
            ]
            labels = [
                dim = 10
                format = "dense"
            ]
        ]
    ]

You can explore different reader settings in the configurations of the [[Examples]]. For details regarding specific readers see the corresponding wiki pages:

* [CNTK Text Format Reader](./BrainScript-CNTKTextFormat-Reader)
* [UCI Fast Reader (deprecated)](./BrainScript-UCI-Fast-Reader)
* [HTKMLF Reader](./BrainScript-HTKMLF-Reader)
* [LM Sequence Reader](./BrainScript-LM-Sequence-Reader)
* [LU Sequence Reader](./BrainScript-LU-Sequence-Reader)
