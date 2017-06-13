---
title:   Enabling 1-bit Stochastic Gradient Descent for the Microsoft Cognitive Toolkit
author:    chrisbasoglu
date:    03/21/2017
ms.author:   cbasoglu
ms.date:   03/21/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Enabling 1-bit Stochastic Gradient Descent for the Microsoft Cognitive Toolkit (1bit-SGD)

***On January 25, 2017 1bit-SGD code is moved to a new Repository in GitHub.***

***If you cloned CNTK Repository with 1bit-SGD enabled prior to January 25, 2017 you need to update git submodule configuration as described in [this article](./Update-1bit-SGD-Submodule-Location.md).***

1-bit Stochastic Gradient Descent (1bit-SGD) is an optional component of the Microsoft Cognitive Toolkit (CNTK) which significantly improves the performance during deep neural network (DNN) training using a single server with multiple GPUs and/or multiple servers with a single or multiple GPUs. This is achieved by the implementing the Gradient Quantization approach that significantly reduces the communication costs during parallel computations involved in the DNN training process. You may read more about 1-bit Stochastic Gradient Descent in this [article](http://research.microsoft.com/apps/pubs/?id=230137).

## License difference between CNTK and 1bit-SGD

1bit-SGD is distributed under a different license than the rest of CNTK. Before considering usage of 1bit-SGD with CNTK please get yourself familiar with both licenses (see links below). Note, that you may find 1bit-SGD license more restrictive than CNTK license:

* [CNTK License](https://github.com/Microsoft/CNTK/blob/master/LICENSE.md)
* [CNTK 1bit-SGD License](./CNTK-1bit-SGD-License.md)

## Setting up 1-bit Stochastic Gradient Descent for CNTK on your system

By using default setup procedures and parameters described in the [main installation section](./Setup-CNTK-on-your-machine.md) your system will contain neither 1bit-SGD code nor the corresponding functionality in compiled binaries.

Please follow the procedure below to enable 1bit-SGD on your system:

### Making aware of Licensing Differences

Please ensure you understand that 1bit-SGD is licensed under a different license than the rest of CNTK (see [License difference between CNTK and 1bit-SGD](#license-difference-between-cntk-and-1bit-sgd)).

### Set up prerequisites

Ensure you set up all required software prerequisites for your platform as described in the [main installation section](./Setup-CNTK-on-your-machine.md). You may skip the part of getting the CNTK code - this will be covered in the next section.

### Getting 1bit-SGD source code (together with or in addition to the main CNTK code)

1bit-SGD source code is stored in a separate repository which is set as a submodule to the main CNTK repository. There are two methods of getting the code depending on your situation.

#### Making a new CNTK repository clone with 1bit-SGD code

This method works if you want to make a *new* clone of CNTK repository.

**IMPORTANT!** Do not apply the command below to an existing repository clone - you will get an error message. If you already have a clone of CNTK repository you are working with and would like to extend it with 1bit-SGD code, proceed to the [next section](#adding-1bit-sgd-code-to-an-existing-cntk-repository-clone). 

To get a new CNTK repository clone with 1bit-SGD source code on your machine you need to use *git recursive clone*:
```
git clone --recursive https://github.com/Microsoft/cntk/
```

You will find 1bit-SGD code in ```Source/1BitSGD``` directory of the CNTK repository.

#### Adding 1bit-SGD code to an existing CNTK repository clone

If you already have a working clone of CNTK repository and would like to expand it with 1bit-SGD perform the following set of commands:

* Change directory to the root of CNTK repository on your machine, e.g.
```
cd /usr/cntk-user/git/repos/cntk
```
or
```
cd c:\code\git\repos\cntk
```
* Execute
```
git submodule update --init -- Source/1BitSGD
```

You will find 1bit-SGD code in `Source/1BitSGD` directory of the CNTK repository.

### Building CNTK binaries with 1bit-SGD functionality

Having 1bit-SGD code on your machine does not automatically mean you have 1bit-SGD functionality in your binaries. To invoke the build with 1bit-SGD perform the following:

#### Windows

Add the Environment Variable ```CNTK_ENABLE_1BitSGD``` and set its value to ```true```. E.g. run this in an Admin (elevated) CMD prompt, and then reopen your CMD shell(s) and Visual Studio to pick up the change:
```
setx CNTK_ENABLE_1BitSGD true
```
If you already built CNTK, you just need to rebuild the SGDLib Project (right-click, Project Only -> Rebuild Only SGDLib).

#### Linux

Pass the option ```--1bitsgd=yes``` to the ```configure``` command.

Then perform the build as usual.

**Next steps**
* [CNTK usage from Brainscript](./CNTK-usage-overview.md)
* [Examples](./Examples.md)
* [Config file overview](./BrainScript-Config-file-overview.md)
