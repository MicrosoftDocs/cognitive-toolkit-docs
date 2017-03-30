Use the [`CloneFunction`](./CloneFunction)`()` operation. E.g.:

    BrainScriptNetworkBuilder = {
        smallnet = BS.Network.Load ("pathToYourModel")
        subnet1 = CloneFunction (smallnet.input, smallnet.output, parameters="learnable" )
        subnet2 = CloneFunction (smallnet.input, smallnet.output, parameters="constant" )
        subnet3 = CloneFunction (smallnet.input, smallnet.output, parameters="shared" )
        subnet4 = CloneFunction (smallnet.input, smallnet.output, parameters="shared" )
        # now use subnet1, subnet2, subnet3, and subnet4 as regular Brainscript functions
    }

`subnet1()` will get a copy of its learnable parameters, which will continue to be updated in training.
`subnet2()` will get a read-only copy of learnable parameters.
`subnet3()` and `subnet4()` will share the same set of learnable parameters, which will get updated jointly during training.