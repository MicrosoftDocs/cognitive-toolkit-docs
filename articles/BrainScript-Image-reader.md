---
title:   BrainScript Image Reader
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   04/14/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   brainscript
---

# BrainScript Image Reader

CAUTION: we still support this reader format, but it is being deprecated. Please use the new 
reader format: [Understanding-and-Extending-Readers](./BrainScript-and-Python---Understanding-and-Extending-Readers.md). 

Image reader can be used to read images and apply some image-specific transforms like random cropping, scaling, horizontal flipping and others. It takes as an input a simple text file where each line contains a tab-separated mapping between image file (e.g. JPEG, PNG etc) and 0-based label. For example:
```
/home/user/data/train/n01440764/n01440764_10026.JPEG<tab>0
/home/user/data/train/n01440764/n01440764_10027.JPEG<tab>0
```

ImageReader supports "..." syntax in the mapping file. If the file path stored in the map file starts with "...", then "..." is substituted by the directory of the mapping file itself.

For example, if a file *C:\work\CNTK\data\train.map* contains
```
...\images\img1.png 1
...\images\img2.png 2
...\images\img3.png 3
```

then the reader will read files as if the following absolute path was specified:
```
C:\work\CNTK\data\images\img1.png 1
C:\work\CNTK\data\images\img2.png 2
C:\work\CNTK\data\images\img3.png 3
```

As some image datasets can be pretty large (e.g. millions of images), it is sometimes beneficial to put these images into a container. Image reader currently supports one container type: ZIP files. In this case it is recommended to use STORE compression mode as it provides fastest access to the contents of the .zip file, also, images usually do not require compression anyway. For example (note the @ sign at the end of .zip file name):
```
/home/user/data/train.zip@/n01440764/n01440764_10026.JPEG<tab>0
/home/user/data/train.zip@/n01440764/n01440764_10027.JPEG<tab>0
/home/user/data/train_01.zip@/n01534433/n01534433_7285.JPEG<tab>0
/home/user/data/train/n01534433/n01534433_7285.JPEG<tab>0
```
Note that you can mix-and-match containers and files, however, it is recommended to limit the number of containers for the better performance i.e. put all images into a single (or few) .zip files.

Please also note that in Windows environment Unicode characters in file names are **not** supported (all characters should be standard ASCII). This limitation comes from OpenCV library that Image reader relies on. 

The following example demonstrates usage of `ImageReader`:
```
    reader=[
        readerType=ImageReader
        file=$ConfigDir$/train_map.txt
        randomize=Auto
        features=[
            # Below are the required parameters.
            width=224
            height=224
            channels=3
            # Below are the optional parameters.
            # Possible values: Center, RandomSide, RandomArea, MultiView10. Default: Center
            cropType=RandomSide
            # Horizontal random flip, will be enabled by default if cropType=Random
            #hflip=0
            # Crop scale side ratio. Examples: sideRatio=0.9, sideRatio=0.7:0.9.
            sideRatio=0.875
            # Crop scale ratio jitter type.
            # Possible values: None, UniRatio. Default: None
            jitterType=UniRatio
            # Interpolation to use when scaling image to width x height size.
            # Possible values: nearest, linear, cubic, lanczos. Default: linear.
            interpolations=Linear
            # Aspect ratio jitter radius. Default is 1.0 (crop square).
            aspectRatio=0.75:1.0
            # Brightness, contrast and color jittering. Default is 0.0 (no effect).
            brightnessRadius=0.2
            contrastRadius=0.2
            saturationRadius=0.4
            # Intensity jittering: enabled if file is specified and intensityStdDev > 0. 
            # The file stores 1x3 vector (eigenvalues) and 3x3 matrix (eigenvectors) in OpenCV XML format.
            intensityFile="$ConfigDir$/ImageNet1K_intensity.xml"
            # StdDev for intensity jittering. Default is 0.0. 
            intensityStdDev=0.1
            # Mean subtraction: enabled if file is specified.
            # The file stores mean values for each pixel in OpenCV matrix XML format.
            meanFile=$ConfigDir$/ImageNet1K_mean.xml
        ]
        labels=[
            labelDim=1000
        ]
    ]    
```
There are very few mandatory parameters, like `width`, `height` and `channels` and several optional parameters which configure image transformations. 

## Crop transform
`cropType` specifies which part of the image is cropped and currently can be `Center`, `RandomSide`, `RandomArea` or `Multiview10`. `RandomSide` and `RandomArea` is usually used during training while `Center` is usually used during testing. Random cropping is a popular data augmentation technique used to improve generalization of the DNN. `MultiView10` is usually used during testing/evaluation phase and enables 10-view image evaluation: (4(corners) + 1(center)) * 2(original + horizontal flip) = 10.

## Horizontal flip transform
`hflip` parameter specifies whether the image will be randomly flipped in horizontal direction. Horizontal flipping is another popular data augmentation technique and should be used if images exhibit vertical symmetry, for example, like many real-world objects.

## Scale transforms
`sideRatio` specifies the ratio of final image dimension, e.g. `width` or `height`, to the size of the random crop taken from the image. For example, the ratio 224 / 256 = 0.875 means crop of size 224 will be taken from the image rescaled to 256 (implementation detail: `ImageReader` takes the crop and then rescales instead of doing the other way around).
To enable scale jitter (another popular data augmentation technique), use colon-delimited values like `sideRatio=0.875:0.466` which means 224 crop will be taken from images randomly scaled to have size in [256, 480] range.

`areaRatio` specifies the ratio of final image area to the original image. For example, an ratio of 0.5 means the crop window area is about half of the original image. The cropped window will then be scaled to 224x224. `sideRatio` (specified together with `cropType=RandomSide`) and `areaRatio` (specified together with `cropType=RandomArea`) are mutually exclusive. 

`aspectRatio` allows one to apply aspect ratio random jitter. For example, a value of 0.2 means aspect ratio of the image can be randomly changed (uniform distribution) to be 80% to 120% of the original.

## Color jitter transforms
`brightnessRadius` and `contrastRadius` allow to apply brightness and contrast jitter. These transforms apply the following formula to every pixel of an image: `Yij = alpha * Xij + beta` where `alpha` is a contrast adjustment and `beta` - brightness. For example, if `contrastRadius` is `0.2` then alpha will be randomly chosen (uniform distribution) from `0.8` to `1.2`. `brightnessRadius` randomly selects values as a proportion of the image mean, for example, `brightnessRadius` equals `0.2` randomly selects values from `-0.2 * mean` to `0.2 * mean`.

`saturationRadius` allows to apply saturation random jitter. For example, a value of `0.3` means saturation of the image can be changed from 70% to 130% of the original.

`intensityFile` and `intensityStdDev` allow to apply PCA-based intensity jitter as described in [this paper](http://papers.nips.cc/paper/4824-imagenet-classification-with-deep-convolutional-neural-networks.pdf).
`intensityFile` is a path to a file which contains eigenvalues and eigenvectors in OpenCV XML format. `intensityStdDev` specifies a standard deviation of random coefficients of eigenvalues.

## Mean subtraction transform
`meanFile` parameter is a path to a file in OpenCV XML matrix format which contains per-pixel mean of the whole dataset. For example, for the input layer of size `224x224x3` the file will contain 150528 entries which will be subtracted during the training (or testing) from the input image. The same or similar effect can be achieved by using few other techniques, for example, `Mean` node or even just subtracting some fixed value, like 128, from the input.
