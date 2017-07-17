---
title:   Pooling 
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   09/15/2016
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# Pooling

    Pooling (input,
             poolKind, # "max" or "average"
             {kernel dimensions}, 
             stride = {stride dimensions}, 
             autoPadding = {padding flags (boolean)},
             lowerPad = {lower padding (int)},
             upperPad = {upper padding (int)})

The pooling operations compute a new matrix by selecting the maximum (max pooling) or average value in the pooling input. In the case of average pooling, count of average does not include padded values. 

N-dimensional pooling allows to create max or average pooling of any dimensions, stride or padding. The syntax is:

where:
* `input` - pooling input
* `poolKind` - "max" or "average"
* `{kernel dimensions}` - dimensions of the pooling window, as a BrainScript vector, e.g. `(4:4)`.
* `stride` - [named, optional, default is 1] strides.
* `autoPadding` - [named, optional, default is true] automatic padding flags for each input dimension.
* `lowerPad` - [named, optional, default is 0] precise lower padding for each input dimension
* `upperPad` - [named, optional, default is 0] precise upper padding for each input dimension

All dimensions arrays are colon-separated. Note: If you use the deprecated `NDLNetworkBuilder`, these must be comma-separated and enclosed in `{ }` instead.

Since the pooling window can have arbitrary dimensions, this allows to build various pooling configurations, for example, a "Maxout" layer (see [Goodfellow et al](http://arxiv.org/abs/1302.4389) for details):

    MaxOutPool (inp, kW, kH, kC, hStride, vStride) =
        Pooling (inp, "max", (kW:kH:kC), stride=(hStride:vStride:kC), true:true:false))

## Simplified syntax for 2D pooling
There is a simplified syntax for 2D pooling:

    MaxPooling(m, windowWidth, windowHeight, stepW, stepH, imageLayout="cudnn" /* or "HWC"*/ )
    AveragePooling(m, windowWidth, windowHeight, stepW, stepH, imageLayout="cudnn" /* or "HWC"*/ )

with the following parameters:
* `m` - the input matrix.
* `windowWidth` - width of the pooling window
* `windowHeight` - height of the pooling window
* `stepW` - step (or stride) used in the width direction
* `stepH` - step (or stride) used in the height direction
* `imageLayout` - [named optional] the storage format of each image. This is a legacy option that you likely won't need. By default it’s `HWC`, which means each image is stored as `[channel, width, height]` in column major notation. For better performance, it is recommended to use cuDNN in which case you should set it to `cudnn`, which means each image is stored as [width, height, channel] in column major notation. Note that `cudnn` format works both on GPU and CPU.

**Example (ConvReLULayer NDL macro):**

    # pool2
    pool2W = 2
    pool2H = 2
    pool2hStride = 2
    pool2vStride = 2
    pool2 = MaxPooling (conv2, pool2W, pool2H, pool2hStride, pool2vStride, imageLayout="$imageLayout$")

Note: If you are using the deprecated `NDLNetworkBuilder`, the optional `imageLayout` parameter defaults to `"HWC"` instead.
