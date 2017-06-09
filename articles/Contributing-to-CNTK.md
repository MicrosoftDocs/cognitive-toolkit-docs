---
title:   How to contribute to CNTK
author:    chrisbasoglu
date:    02/17/2016
ms.author:   cbasoglu
ms.date:   02/17/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# How to contribute to CNTK

You want to contribute to CNTK? We're really excited to work together!

*Please note, that the information on this page is likely to change as we add more services to our GitHub Repository. So we recommend you to check this page every time you want to make a contribution.*

*Last major page update: 17-Feb-2016*

Here are the simple steps you need to follow to see your code as a part of CNTK:

## Preliminary Information

* Note that in most cases you will be required to accept Microsoft Contribution License Agreement (CLA) *before you contribution is reviewed*. You may study the text of the agreement [here](https://cla.microsoft.com/cladoc/microsoft-contribution-license-agreement.pdf). You will be automatically notified whether you need to accept CLA after you make a Pull Request (see below). The procedure is automated and should not take more, than 5-7 minutes. Also, you will have to accept the CLA only **once**, and we will not bother you with this during the subsequent contributions
* Please, make each contribution reasonably small - it will allow us to review and accept it quicker. Also if you would like to improve several points, divide it in separate Pull Requests
* If you would like to make **a really big** contribution, like develop a brand new feature of CNTK, please, **consult us preliminary** by [raising an issue](https://github.com/Microsoft/CNTK/issues). We value your cooperation and respect your time and thus want to ensure that we're ready for your work
* Please, get yourself familiar with [Developing and Testing](./Developing-and-Testing.md) and especially [Coding Guidelines](./Coding-Guidelines.md) sections of the CNTK documentation.

## Making a contribution

* [Fork CNTK Repository](https://help.github.com/articles/fork-a-repo/)
* Code your contribution in the fork just created
* To make a contribution create a [GitHub Pull Request](https://help.github.com/articles/creating-a-pull-request/) using [Comparing across forks view](https://help.github.com/articles/comparing-commits-across-time/#comparing-across-forks). Use ```Microsoft/CNTK``` for ```base fork``` and  ```master``` branch for ```base```
* Please provide a short description of your contribution while creating the Pull Request
* If asked accept CLA (see previous section). **Please note, that we can NOT start reviewing your contribution until CLA is in place or is in state "*cla-not-required*".**
* We will *start* reviewing the Pull Request in no later than *two business days*. Note, that the actual length of the review depends of the nature of the proposed change and may take longer. You will see the comments in the Pull Request as it goes along
* We ask to ensure that your branch has no merge conflicts with ```master``` (GitHub Pull Request web interface informs you about it). We ask to ensure this conflict-free state both *before* and *after* the contribution review. (I.e. in case during the time of the review on-going ```master``` updates result in a merge conflict, we will ask you to resolve it and make a new commit before we proceed with  the integration)
* If the contribution is accepted and in merge-conflict-free state, it will be merged into the ```master``` branch

That's it! We're looking forward getting your contribution!
