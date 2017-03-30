## Evaluating a model using cntk.exe

Evaluating a model using the CNTK executable itself, i.e. cntk.exe, is similar to the training process. But instead of using the "train" command, the "eval" command is placed in the configuration file. 

### Using the CNTK executable for evaluation has the following advantages:
#### CPU/GPU capability
Like training, CNTK can leverage the GPU during evaluation. Refer to the [Config File Overview](./Config-file-overview) page for more details.
#### Readers (and their transformations)    
Similar to model training, the reader plugins (e.g. ImageReader) may perform some data transforms on the input data prior to feeding it to the network during training. These transforms are not part of CNTK (per se), but of the readers. In order to feed the same *transformed* data during evaluation, the transformations need to occur prior to feeding. When evaluating using the CNTK executable, the same reader (as used during evaluation) can be used, and thus the same transformation can be applied. As we will cover later in this page, when using the programmatic approach, these transforms will need to be performed programmatically outside of evaluation engine prior to submitting the data for evaluation (assuming the model was trained with transformed data).    
#### Model tweaking
When using CNTK for evaluation, there is a possibility of modifying the model's layout using BrainScript. This enables additional capabilities, such as exposing hidden layers for evaluation. Refer to the BrainScript page for more information.    

### Using the CNTK executable for evaluation has the following disadvantages:
#### Process spin-up time
The CNTK executable (by nature) runs as a process, and thus will take some time to spin up. For services where many requests need to be dynamically processed, the better option would be to use the Evaluation Library in a service.
#### File based input/output
The CNTK executable reads the input data from file(s) and writes the output data to a file. For services running in the cloud, this may cause some performance issues.

**Note: If you do go the route of evaluating a CNTK model with the CNTK executable, make sure your parameters are adequate for the evaluation. In particular specifying an appropriate size for the MiniBatchSize. Please refer to the [[Troubleshoot CNTK]] page for more information.**