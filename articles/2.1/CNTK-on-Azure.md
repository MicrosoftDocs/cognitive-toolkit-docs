---
title:   CNTK on Azure
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   03/22/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK on Azure

CNTK is available as [Microsoft Azure](http://azure.microsoft.com/) Virtual Machine offerings.

## Host a CNTK Model through Web APIs in Azure
[Deploy CNTK Azure Web API](./Evaluate-a-model-in-an-Azure-WebApi.md)

## Training with CNTK on Azure with Windows

* [Data Science Virtual Machine](https://azure.microsoft.com/en-us/marketplace/partners/microsoft-ads/standard-data-science-vm/)
* [Data Science Virtual Machine Documentation](https://azure.microsoft.com/en-us/documentation/articles/machine-learning-data-science-provision-vm/)
* If you would like to use Microsoft Azure GPU Capability, be sure to read the correspondent section at the end of this page

## Training with CNTK on Azure with Linux

* [Data Science Virtual Machine](https://azure.microsoft.com/en-us/marketplace/partners/microsoft-ads/linux-data-science-vm/)
* [Data Science Virtual Machine Documentation](https://azure.microsoft.com/en-us/documentation/articles/machine-learning-data-science-linux-dsvm-intro/)
* If you would like to use Microsoft Azure GPU Capability, be sure to read the correspondent section at the end of this page
* We recommend to get yourself familiar with the [Azure Batch Shipyard](https://github.com/Azure/batch-shipyard) which can provision Dockerized CNTK on [Azure Batch](https://azure.microsoft.com/en-us/services/batch/) compute nodes with the ability to execute on CPU, GPU, multinode CPU and multinode GPU with simple configuration files. CNTK recipes can be found [here](https://github.com/Azure/batch-shipyard/tree/master/recipes)

## Using Azure GPU

If you would like using Azure GPU capability for your Virtual Machines, you need to use [Azure Deep Learning Extension](http://aka.ms/dsvm/deeplearning). 

### Installing GPU Drivers for Azure
Also you need to ensure that you have NVIDIA drivers installed on your Virtual Machine. See OS specific details in the following articles:   
* [Windows](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-windows-n-series-driver-setup?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Linux](https://docs.microsoft.com/en-us/azure/virtual-machines/virtual-machines-linux-n-series-driver-setup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
