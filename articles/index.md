# The Microsoft Cognitive Toolkit

The Microsoft Cognitive Toolkit - CNTK - is a unified deep-learning toolkit by Microsoft Research. [This video](https://youtu.be/9gDDO5ldT-4) provides a high-level view of the toolkit. 

**Important Note:** there are **BREAKING** changes in master compared to beta15, click [here](./Breaking-changes-in-Master-compared-to-beta15) for more information.

It can be included as a library in your Python or C++ programs, or used as a standalone machine learning tool 
through its own model description language (BrainScript).

CNTK supports 64-bit Linux or 64-bit Windows operating systems. To install you can either choose pre-compiled 
binary packages, or compile the Toolkit from the source provided in Github.

Here are a few pages to get started:
* [Setting up CNTK on your machine](./Setup-CNTK-on-your-machine)
* [Tutorials, Examples, etc..](/en-us/cognitive-toolkit/Tutorials,-Examples,-etc...md)
  * Try the [tutorials on Azure Notebooks](https://notebooks.azure.com/CNTK/libraries/tutorials) with **pre-installed** CNTK 
* [The CNTK Library APIs](./CNTK-Library-API)
  * [Using CNTK from Python](https://www.cntk.ai/pythondocs)
  * [Using CNTK from C++](./CNTK-Library-API#c-api)
* CNTK as a machine learning tool through [BrainScript](./Using-CNTK-with-BrainScript)
* [How to contribute to CNTK](./Contributing-to-CNTK)
* Give us feedback through these [channels](./Feedback-Channels).

Note to search the pages of this Wiki, in the search box, type: Language:Markdown yourSearchText 

This Wiki is the most up-to-date information about the Microsoft Cognitive Toolkit. For more background 
refer to the tutorials provided. A general introduction to computational networks and the core 
algorithms in CNTK, or to cite the work, please refer to the Microsoft Technical Report MSR-TR-2014-112:
["An Introduction to Computational Networks and the Computational Network Toolkit"]
(http://research.microsoft.com/apps/pubs/?id=226641). The source of this report is in the Git repository 
[folder](https://github.com/Microsoft/CNTK/tree/master/Documentation/CNTK-TechReport/lyx).  
It is updated less frequently and shouldn't be used the most up-to-date source of information.


### What's New
To update to the latest CNTK release, use: 
```
pip install --upgrade https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0.beta15.0-cp35-cp35m-linux_x86_64.whl
```
or follow more detailed installation instruction [here](./Setup-CNTK-on-your-machine). 

***2017-03-16.* V 2.0 Beta 15 Release available at Docker Hub**  
CNTK V 2.0 Beta 15 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [Wiki article](./CNTK-Docker-Containers).

***2017-03-15.* V 2.0 Beta 15 Release**  
Highlights of this Release:
* In addition to pre-existing python support, added support for TensorBoard output in BrainScript. [Read more here](./Using-TensorBoard-for-Visualization).
* Learners can now be implemented in pure Python by means of `UserLearners`. [Read more here](https://www.cntk.ai/pythondocs/extend.html#user-learners).
* New debugging helpers: `dump_function()`, `dump_signature()`.
* Tensors can be indexed using advanced indexing. E.g. `x[0,2,3](/en-us/cognitive-toolkit/0,2,3.md)` would return a tensor that contains the first, third and fourth element of the first axis.
* Significant updates in the [Layers Library](https://www.cntk.ai/pythondocs/layerref.html) of Pythin API. See [Release Notes](./CNTK_2_0_beta_15_Release_Notes) for detailed description.
* Updates and [new examples](./CNTK-Eval-Examples#examples-for-evaluating-multiple-requests-in-parallel) in C# API.
* Various bug fixes.

See more in the [Release Notes](./CNTK_2_0_beta_15_Release_Notes).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

***2017-02-28.* V 2.0 Beta 12 Release available at Docker Hub**  
CNTK V 2.0 Beta 12 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [Wiki article](./CNTK-Docker-Containers).

***2017-02-23.* V 2.0 Beta 12 Release**  
Highlights of this Release:
* New and updated features: new activation functions, support of `Argmax` and `Argmin`, improved performance of `numpy` interop, new functionality of existing operators, and more.
* [CNTK for CPU on Windows can now be installed via `pip install` on Anaconda 3](./Setup-CNTK-on-your-machine). Other configurations will be enabled soon.
* HTK deserializers are now exposed in Python. All deserializers are exposed in C++.
* The memory pool implementation of CNTK has been updated with a new global optimization algorithm. Hyper memory compression has been removed.
* New features in C++ API.
* [New Eval examples for RNN models](https://github.com/Microsoft/CNTK/blob/master/Examples/Evaluation/CNTKLibraryCSEvalCPUOnlyExamples/CNTKLibraryCSEvalExamples.cs).
* New [CNTK NuGet Packages](./NuGet-Package) with CNTK V2 C++ Library.

See more in the [Release Notes](./CNTK_2_0_beta_12_Release_Notes).  
Get the Release from the [CNTK Releases page](https://github.com/Microsoft/CNTK/releases).

***2017-02-13.* V 2.0 Beta 11 Release available at Docker Hub**  
CNTK V 2.0 Beta 11 Runtime packages are now available as [Public Images at Docker Hub](https://hub.docker.com/r/microsoft/cntk/).  
See more on CNTK as Docker Images in this [Wiki article](./CNTK-Docker-Containers).

See [all news](./News).
