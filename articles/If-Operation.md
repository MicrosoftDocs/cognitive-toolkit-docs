---
title:   If operation
author:    chrisbasoglu
date:    08/27/2016
ms.author:   cbasoglu
ms.date:   08/27/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# If operation

Elementwise selecting of one of two inputs given a condition.

    BS.Boolean.If (condition, thenValue, elseValue)

## Parameters

* `condition`: condition according to which element values are selected
* `thenValue`: element value selected if `condition` element is not 0
* `elseValue`: element value selected if `condition` element is 0

Sparse values are currently not supported.

## Return Value

A tensor of the dimension of the inputs. If any of the inputs have dimensions of 1,
broadcasting is applied; in that case, the output dimension becomes the maximum over the corresponding three arguments' dimensions.

## Description

`If()` selects elements from two inputs based on a condition, in an elementwise fashion.
For every input element where `condition` is non-0, the corresponding element from `thenValue`
is chosen; and where `condition` is 0, the corresponding `elseValue` element is chosen.

This function supports broadcasting. For example, it is possible that the condition
is a scalar, or one of the inputs is a constant tensor without time dimension.

## Example
The elementwise maximum of two inputs can be computed as a combination of [`Greater()`](./Binary-Operations.md) and `If()`:

    MyElementwiseMax (a, b) = BS.Boolean.If (Greater (a, b), a, b)

This also works with broadcasting. For example, the linear rectifier can be written with this using
a scalar constant as the second input:

    MyReLU (x) = MyElementwiseMax (x, Constant(0))
