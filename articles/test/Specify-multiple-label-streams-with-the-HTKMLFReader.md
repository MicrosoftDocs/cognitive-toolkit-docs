The HTKMLFReader (the reader to read Master Label Files (MLF) of the Hidden Markov Toolkit (HTK))
can be configured to read multiple label streams. The example below is taken from 
[TIMIT_TrainMultiTask_ndl_deprecated.cntk](https://github.com/Microsoft/CNTK/blob/master/Examples/Speech/Miscellaneous/TIMIT/config/TIMIT_TrainMultiTask_ndl_deprecated.cntk)
in the Examples directory:

    reader = {
        readerType = "HTKMLFReader"
        ...
        labels = {
            mlfFile = "$MlfDir$/TIMIT.train.align_cistate.mlf.cntk"
            labelMappingFile = "$MlfDir$/TIMIT.statelist"
            labelDim = 183
            labelType = "category"
        }
        regions = {
            mlfFile = "$MlfDir$/TIMIT.train.align_dr.mlf.cntk"
            labelDim = 8
            labelType = "category"
        }
    }

