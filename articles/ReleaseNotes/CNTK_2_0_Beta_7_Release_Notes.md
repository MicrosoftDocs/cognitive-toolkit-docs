## CNTK v.2.0 Beta 7 Release Notes

This is a summary of new features delivered with the Beta 7 release of CNTK V.2.0.

### Python API

In the past, the Python API was very permissive in what it accepted as data input types. This caused a lot of confusion (e.g. non-sequence data being wrongly then interpreted as sequences). Therefore we have made the Python API stricter. In particular:

* Sequences now have to be provided as single NumPy or Scipy sparse arrays. I.e., it is not any more allowed to provide a sequence as a list of NumPy arrays or Scipy sparse arrays.
* The helper function ```cntk_device``` has been hidden in the testing modules. For device selection use the module ```cntk.device```
* Python default initialization is set to *normal* (```std=1```) and *uniform* (```[-1,1]```).
* ```gaussian``` is renamed to ```normal```.

### New Examples and Tutorials

We have prepared the following Examples and Tutorials:

* [Artistic Style Transfer](https://github.com/Microsoft/CNTK/blob/v2.0.beta7.0/Tutorials/CNTK_205_Artistic_Style_Transfer.ipynb)
* [GoogLeNet (Inception V3)](https://github.com/Microsoft/CNTK/tree/v2.0.beta7.0/Examples/Image/Classification/GoogLeNet)

### C++ / Backend

We have made some changes and improvements in CNTK Backend:

* Changed data distribution in multi-GPU training for the composite reader in the frame mode
* Improved performance in the composite reader to use all available CPU threads during deserialization/transformation
* Random number generator and distributions were updated at many places to be the same across supported platforms (Linux and Windows)
  * Backward compatibility note: training (in particular initialization) may not be exactly reproducible with respect to the previous versions
* Implemented an optimization to elide the initial zeroing and subsequent accumulation into the gradients for nodes with just one parent/ancestor node 

### CNTK Evaluation library. NuGet package 

A new Nuget package with the latest eval DLL (managed and native) is [available](https://github.com/Microsoft/CNTK/wiki/NuGet-Package). 

 **IMPORTANT!** In Visual Studio *Manage Nuget Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the package will not be visible. The Package version should be ```2.0-beta7```.