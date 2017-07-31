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

# ROIPooling
```
    ROIPooling (input,
                ROIs,
                {ROI output shape}, 
                spatialScale = {spatial scale wrt image (float)})
```
The ROI-pooling operation computes a new matrix by selecting the maximum (max pooling) value in the pooling input for each region of interest (ROI). 
The regions of interest are given as the second input to the operator as the top left and bottom right corners of the regions in absolute pixels of the original image. 
The pooling input is computed per ROI by projecting the coordinates onto the input feature map (first input to the operator) and considering all overlapping positions. 
The projection uses the 'spatial scale' which is the size ratio of the input feature map over the input image size. 
The spatial scale can be computed by multiplying all strides that occur before the ROI-pooling and taking the inverse, 
e.g., a network that has four pooling layers with stride two would have a spatial scale of 1/16.
The output shape's width and height are determined by the third argument, the output depth (number of filters) is the same as the input depth.

* `input` - pooling input for the entire image
* `ROIs` - ROI coordinates as absolute pixel coordinates `(x_min, y_min, x_max, y_max)`
* `{roi output shape}` - dimensions (width, height) of the ROI output, as a BrainScript vector, e.g. `(4:4)`.
* `spatialScale` - the scale of operand from the original image size. The default is 1/16, which matches for example AlexNet and VGG16 networks.

> [!NOTE]
> [!INCLUDE[versionchanged-2.1](includes/versionchanged-2.1.md)]
> In CNTK 2.1 the spatial scale parameter was added and the coordinates of the ROIs are now passed as absolute pixel values rather than relative values as in previous versions. 
