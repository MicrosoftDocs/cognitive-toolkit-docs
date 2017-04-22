# Post Batch Normalization Statistics

Post batch normalization statistics (PBN) is the CNTK version of how to evaluate the population mean and variance of Batch Normalization which could be used in inference [Original Paper](https://arxiv.org/pdf/1502.03167v3.pdf).

## Why needs PBN?

Since the parameters keep updating while training, the accuracy of mean and variance, using running statistics, will be impacted as well. However, PBN, calculated after training and all the parameters are frozen, won't influence by these factors. For ResNet, PBN will achieve better performance than running statistics.

## Characters of PBN

PBN has two characters to make sure its performance:
* `based on frozen parameters`: PBN will begin after the training action, meaning all others parameter have been fixed. the mean and variance of each batch normalization layer are the only un-decided parameters in the network. This will ensure the statistics results won't influence by the parameters updating.
* `calculate layer by layer`: PBN imports a bottom to top mean and variance updating method. The mean and variance of a batch normalization layer won't calculate unless the mean and variance of all the bottom batch normalization layers are fixed. This will ensure for each current calculating layer, the network parameters before its order are all fixed, including earlier layers' mean and variance.

## Flow

PBN executes with following steps:
* `1. Load trained model`
* `2. Find all the batch normalization layers`
* `3. Resort them as eval order`
* `4. From the first layer to last layer do`
* `5. set the mean and variance of current batch normalization to 0`
* `6. partial forward from bottom to current node for n times, and average the mean and variance of these n times.`
* `7. set the mean and variance of current batch normalization to the average value`
* `8. move to the next layer, jump to step 5`

## PBN usage

The usage of PBN can find in [PBN Command](./Top-level-commands#pbn-command)
    

