---
title:   ROIPooling 
author:    pkranen
ms.author:   pkranen
ms.date:   07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Pooling

    Pooling (input,
             roiInput,
             poolKind, # "max"
             {roi output dimensions}, 
             spatialScale = {spatial scale wrt image (float)})

The roi-pooling operation computes a new matrix by selecting the maximum (max pooling) value in the pooling input for each region of interest (roi). 
The regions of interest are given as the second input to the operator as the top left and bottom right corners of the regions in absolute pixels of the original image. 
The pooling input is computed per roi by projecting the coordinates onto the input feature map (first input to the operator) and considering all overlapping positions. 
The projection uses the 'spatial scale' which is the size ratio of the input feature map over the input image size. 
The spatial scale can be computed by multiplying all strides that occur before the roi-pooling and taking the inverse, 
e.g., a network that has four pooling layers with stride two would have a spatial scale of 1/16.

* `input` - pooling input for the entire image
* `roiInput` - roi coordinates as absolute pixel coordinates `(x_min, y_min, x_max, y_max)`
* `poolKind` - "max"
* `{roi output dimensions}` - dimensions (width, height) of the roi output, as a BrainScript vector, e.g. `(4:4)`.
* `spatialScale` - the scale of operand from the original image size.

