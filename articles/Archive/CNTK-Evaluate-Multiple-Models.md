---
title:   CNTK Evaluate Multiple Models
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# CNTK Evaluate Multiple Models

## Overview
The CNTK EvalDll library (`Cntk.Eval` and its .Net wrapper `Cntk.Eval.Wrapper` in Windows and `libCntk.Eval` in Linux) enables programmatic single threaded evaluation of CNTK models (concurrent evaluations of a single model instance is not supported).
However, it is possible to load multiple instances of a model and evaluate each model with a single thread. This enables multiple models to be evaluated in parallel, yet each model with a single thread.

## Example
Refer to the `EvaluateMultipleModels` method in the [CSEvalClient](https://github.com/Microsoft/CNTK/tree/release/latest/Examples/Evaluation/LegacyEvalDll/CSEvalClient) program for an example of a possible implementation for evaluating multiple models concurrently.

