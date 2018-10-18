---
title:   Convolution 
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   02/02/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# Convolution


    Convolution (w, input,
                 {kernel dimensions}, 
                 mapDims = {map dimensions}, 
                 stride = {stride dimensions}, 
                 sharing = {sharing flags},
                 autoPadding = {padding flags (boolean)},
                 lowerPad = {lower padding (int)},
                 upperPad = {upper padding (int)},
                 maxTempMemSizeInSamples = 0)

`Convolution()` computes the convolution of a weight matrix with an image or tensor. This operation is used in image-processing applications and language processing. It supports any dimensions, stride, sharing or padding.

This function operates on input tensors of the form `[M1 x M2 x ... x Mn x inChannels]`. This can be understood as a rank-`n` object, where each entry consists of a `inChannels`-dimensional vector.
For example, an RGB image would have dimensions `[W x H x 3]`, i.e. a `[W x H]`-sized structure, where each entry (pixel) consists of a 3-tuple (note, however, that the memory-storage format is the concatenation of 3 planes of size `[W x H]`).

`Convolution()` convolves the input with `n+1`-dimensional filters, where the first `n` dimensions are the spatial extent of the filter, and the last one must be equal to `inChannels`. There are `outChannels` filters. I.e. for each output position, a vector of dimension `outChannels` is computed. Hence, the total number of filter parameters is `(M1*M2*...*Mn) * inChannels * outChannels`.

`Convolution()` has the following parameters:
* `w` - convolution filter weights, stored as a matrix of dimensions `[outChannels, (M1*M2*...*Mn)]`, where `(M1*M2*...*Mn)` must be the product of the kernel dimensions, e.g. `75` for a `[5 x 5]`-sized filter on 3 input channels.
* `input` - convolution input. A tensor with dimensions `[M1 x M2 x ... x Mn x inChannels]`.
* `kernel dimensions` - dimensions of the filters *including* the number of input channels, given as a BrainScript vector. The last dimension must be equal to the number of input channels. E.g. `(5:5:3)` for a `[5 x 5]` filter on a 3-channel input.
* `mapDims` - [named, optional, default is 0] number of output channels (=called `outChannels` above; same as the depth of resulting feature map). 0 means use the row dimension of `w`
* `stride` - [named, optional, default is 1] stride dimensions. A stride > 1 means that only pixel positions that are multiples of the stride value are computed. For example, a stride of 2 will lead to a halving of the dimensions. The last stride dimension that lines up with the number of input channels must be equal to the number of input channels.
* `sharing` - [named, optional, default is true] sharing flags for each input dimension
* `autoPadding` - [named, optional, default is true] flags for each input dimension whether it should be padded automatically (that is, symmetrically) or not padded at all. Padding means that the convolution kernel is applied to all pixel positions, where all pixels outside the area are assumed zero ("padded with zeroes"). Without padding, the kernels are only shifted over positions where all inputs to the kernel still fall inside the area. In this case, the output dimension will be less than the input dimension. The last value that lines up with the number of input channels must be `false`.
* `lowerPad` - [named, optional, default is 0] precise lower padding for each input dimension.
* `upperPad` - [named, optional, default is 0] precise upper padding for each input dimension.
* `maxTempMemSizeInSamples` - [named optional] maximum amount of auxiliary memory (in samples) that should be reserved to perform convolution operations. Some convolution engines (e.g. cuDNN and GEMM-based engines) can benefit from using workspace as it may improve performance. However, sometimes this may lead to higher memory utilization. Default is 0 which means the same as the input samples.

All values of the form `{...}` must actually be given as a colon-separated sequence of values, e.g. `(5:5)` for the kernel dimensions. (If you use the deprecated `NDLNetworkBuilder`, these must be comma-separated and enclosed in `{ }` instead.)

*Example:*

    ConvReLULayer(inp, outMap, inMap, kW, kH, hStride, vStride, wScale, bValue) =
    [
        W = Parameter (outMap, inMap * kW * kH, init="gaussian", initValueScale=wScale)
        b = Parameter (outMap, 1, init="fixedValue", value=bValue)
        c = Convolution (W, inp, (kW:kH:inMap), stride=(hStride:vStride:inMap), autoPadding=(true:true:false))
        y = RectifiedLinear (c + b)
    ].y

Note: If you are using the deprecated `NDLNetworkBuilder`, there should be no trailing `.y` in the example.

## Simplified 2D Convolution (deprecated NDL only)

The now deprecated NDL language provided a simplified 2D variant of `Convolution()`:

    Convolution (w, image, 
                 kernelWidth, kernelHeight, mapDims, 
                 horizontalStride, verticalStride,
                 zeroPadding=false, maxTempMemSizeInSamples=0, imageLayout="cudnn" /* or "HWC"*/ )

where:
* `w` - convolution weight matrix, it has the dimensions of `[mapDims, kernelWidth * kernelHeight * inputChannels]`.
* `image` - the input image.
* `kernelWidth` - width of the kernel
* `kernelHeight` - height of the kernel
* `mapDims` - depth of output feature map (number of output channels)
* `horizontalStride` - stride in horizontal direction
* `verticalStride` - stride in vertical direction
* `zeroPadding` - [named optional] specifies whether the sides of the image should be padded with zeros. Default is false.
* `maxTempMemSizeInSamples` - [named optional] maximum amount of auxiliary memory (in samples) that should be reserved to perform convolution operations. Some convolution engines (e.g. cuDNN and GEMM-based engines) can benefit from using workspace as it may improve performance. However, sometimes this may lead to higher memory utilization. Default is 0 which means the same as the input samples.
* `imageLayout` - [named optional] the storage format of each image. This is a legacy option. By default it’s `HWC`, which means each image is stored as `[channel, width, height]` in column major. If you use cuDNN to speed up training, you should
set it to `cudnn`, which means each image is stored as `[width, height, channel]`. Note that `cudnn` layout will work both on GPU and CPU so it is recommended to use it by default.
