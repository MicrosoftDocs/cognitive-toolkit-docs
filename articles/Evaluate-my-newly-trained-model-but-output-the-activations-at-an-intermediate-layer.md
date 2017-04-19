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
