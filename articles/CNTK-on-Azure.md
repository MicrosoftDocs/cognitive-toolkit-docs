---
title:   CNTK on Azure
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/22/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# CNTK on Azure

CNTK is available as [Microsoft Azure](https://azure.microsoft.com/) Virtual Machine offerings.

## Host a CNTK Model through Web APIs in Azure
[Deploy CNTK Azure Web API](./Evaluate-a-model-in-an-Azure-WebApi.md)

## Training with CNTK on Azure with Windows 

* [Data Science Virtual Machine - Windows Server 2016](https://techcommunity.microsoft.com/t5/educator-developer-blog/windows-server-2016-data-science-virtual-machine/ba-p/378747)
* [Data Science Virtual Machine (Windows) Documentation](/azure/machine-learning/data-science-virtual-machine/provision-vm)


## Training with CNTK on Azure with Linux

* [Data Science Virtual Machine - Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-2004?tab=Overview)
* [Data Science Virtual Machine (Linux) Documentation](https://aka.ms/dsvm/ubuntu/docs)
* We recommend to get yourself familiar with the [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard) which can provision Dockerized CNTK on [Azure Batch](https://azure.microsoft.com/en-us/services/batch/) compute nodes with the ability to execute on CPU, GPU, multinode CPU and multinode GPU with simple configuration files. CNTK recipes can be found [here](https://github.com/Azure/batch-shipyard/tree/master/recipes)

**NOTE:** The Data Science Virtual Machine (DSVM) can be deployed on either CPU only or GPU based VM instances. The NVidia drivers, CUDA, cuDNN is built into the DSVM. 

### Installing GPU Drivers for Azure
Also you need to ensure that you have NVIDIA drivers installed on your Virtual Machine in case you are not using the DSVM. See OS specific details in the following articles:   
* [Windows](/azure/virtual-machines/windows/n-series-driver-setup)
* [Linux](/azure/virtual-machines/linux/n-series-driver-setup)
