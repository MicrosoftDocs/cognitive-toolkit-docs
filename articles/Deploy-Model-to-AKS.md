---
title:   Deploy a Model to Azure Container Service
author:    mx-iao
ms.author:   minxia
ms.date:  12/04/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:  
---

# Deploy a Model to Azure Container Service
The following resources from the [Cortana Intelligence and Machine Learning](https://blogs.technet.microsoft.com/machinelearning/) team go over tutorials for deploying CNTK models to [Azure Container Service](https://azure.microsoft.com/en-us/services/container-service/).

## Deploy Pretrained Models to Azure Container Service
Tutorial: [Deployment of Pretrained Models to Azure Container Service](https://blogs.technet.microsoft.com/machinelearning/2017/05/25/deployment-of-pre-trained-models-on-azure-container-services/)
| [Github](https://github.com/Azure/ACS-Deployment-Tutorial)  
Authors: Mathew Salvaris, Ilia Karmanov

This end-to-end tutorial goes over the process of building a simple image classification web application, using a pretrained CNTK ResNet 152 model, and deploying the app via ACS.

Sections:
* Create Docker image of the application
* Test the application locally
* Create an ACS cluster and deploy the web app
* Test the web app

## Train and Serve Models using Kubernetes on Azure
Tutorial: [How to Serve and Train Deep Learning Models at Scale, using Cognitive Toolkit with Kubernetes on Azure](https://blogs.technet.microsoft.com/machinelearning/2017/09/06/how-to-use-cognitive-toolkit-cntk-with-kubernetes-on-azure/) | [Github](https://github.com/weehyong/k8scntkSamples)  
Author: Wee Hyong Tok

This tutorial explains how to deploy a Kubernetes cluster on Azure using the [ACS Engine](https://github.com/Azure/acs-engine) and how to autoscale the cluster. Once the Kubernetes cluster is up and running, the tutorial [repo](https://github.com/weehyong/k8scntkSamples) provides sample CNTK docker images for training on the CIFAR-10 dataset and serving a pretrained ResNet model (based on the [above](#deploy-a-model-to-azure-container-service) tutorial).
