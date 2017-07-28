---
title:   CNTK Evaluate Image Transforms
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:  07/31/2017
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK Evaluate Image Transforms

This page describes some possible implementations for transforming images prior to evaluating them on a CNTK model that was trained with data fed with the ImageReader. A working example is provided as part of the [CSEvalClient](https://github.com/Microsoft/CNTK/blob/release/2.1/Examples/Evaluation/LegacyEvalDll/CSEvalClient) example program, in particular refer to the `EvaluateImageClassificationModel` method.

## Overview
The CNTK ImageReader plugin enables feeding image data to the CNTK model for training, testing, and evaluation. The ImageReader has some configurable capabilities that when enabled, perform some on-the-fly transforms to the image data.
These possible transforms are:
* Cropping
* Resize
* Applying the Mean
* Intensity 
* Color
* Layout (HWC vs CHW)

## Image evaluation with CNTK.exe and the Image Reader
In this case image transforms can be specified in the configuration file, and the Imagereader will perform the defined transformations.

## Programmatic image evaluation through EvalDll(EvalWrapper)
In this case the required image transformations have to be performed programmatically before the image is passed into the Evalwrapper.

This section provides some possible implementations for performing some of these transformations prior to evaluation.

For example a static class named CntkBitmapExtensions could contain the extension methods shown below.

### Resize
        /// <summary>
        /// Resizes an image
        /// </summary>
        /// <param name="image">The image to resize</param>
        /// <param name="width">New width in pixels</param>
        /// <param name="height">New height in pixesl</param>
        /// <param name="useHighQuality">Resize quality</param>
        /// <returns>The resized image</returns>
        public static Bitmap Resize(this Bitmap image, int width, int height, bool useHighQuality)
        {
            var rect = new Rectangle(0, 0, width, height);
            var newImg = new Bitmap(width, height);

            newImg.SetResolution(image.HorizontalResolution, image.VerticalResolution);

            using (var g = Graphics.FromImage(newImg))
            {
                g.CompositingMode = System.Drawing.Drawing2D.CompositingMode.SourceCopy;
                if (useHighQuality)
                {
                    g.InterpolationMode = System.Drawing.Drawing2D.InterpolationMode.HighQualityBicubic;
                    g.CompositingQuality = System.Drawing.Drawing2D.CompositingQuality.HighQuality;
                    g.SmoothingMode = System.Drawing.Drawing2D.SmoothingMode.HighQuality;
                    g.PixelOffsetMode = System.Drawing.Drawing2D.PixelOffsetMode.HighQuality;
                }
                else
                {
                    g.InterpolationMode = System.Drawing.Drawing2D.InterpolationMode.Default;
                    g.CompositingQuality = System.Drawing.Drawing2D.CompositingQuality.Default;
                    g.SmoothingMode = System.Drawing.Drawing2D.SmoothingMode.Default;
                    g.PixelOffsetMode = System.Drawing.Drawing2D.PixelOffsetMode.Default;
                }

                var attributes = new ImageAttributes();
                attributes.SetWrapMode(System.Drawing.Drawing2D.WrapMode.TileFlipXY);
                g.DrawImage(image, rect, 0, 0, image.Width, image.Height, GraphicsUnit.Pixel, attributes);
            }

            return newImg;
        }

In this case a possible invocation could be:

     var testBitmap = new Bitmap(Bitmap.FromFile(@"C:\rocket.bmp")).Resize(224, 224, true);

This command would resize the `C:\rocket.bmp` image to a size of 224 x 224 pixels maintaining a high quality image.

### Layout conversion from HWC to CHW
There are primarily two layout types used in CNTK: HWC and CHW.
The first, HWC is the default format used in CNTK. The second, CHW, is the format used by cuDNN in the GPU.

**Note, the actual *file* layout *may* be different. We are looking at the memory representation, not the file content**

**Note, the descriptions above refer to the commonly used _row-major_ notation where the fastest moving dimension comes last. CNTK usually uses _column-major_ notation that uses the fastest moving dimension first and where the below would be expressed as "CWH" and "WHC", respectively.**

This means that assuming a bitmap with HWC format of size 10x10 using RGB bytes, the memory space would correspond to:

    Offset (byte) :  0  1  2  3  4  5  6  7  8 ...29 30 31 32 33 34 35 36 37 ...
    Height Pos    :  0  0  0  0  0  0  0  0  0 ... 0  0  0  1  1  1  1  1  1 ...
    Width Pos     :  0  0  0  1  1  1  2  2  2 ... 9  9  9  0  0  0  1  1  1 ...
    Color Index   :  B  G  R  B  G  R  B  G  R ... B  G  R  B  G  R  B  G  R ...

In the case of CHW the layout would be:

    Offset (byte) :  0  1  2  3 ... 9 10 11 12 13 ...90 91 92 93 ... 99 100 ... 199 200 ... 299 
    Color Index   :  B  B  B  B ... B  B  B  B  B ... B  B  B  B ...  B   G ...   G   R ...   R
    Height Pos    :  0  0  0  0 ... 0  0  0  0  0 ... 9  9  9  9 ...  9   0 ...   9   0 ...   9
    Width Pos     :  0  1  2  3 ... 9  0  1  2  3 ... 0  1  2  3 ...  9   0 ...   9   0 ...   9

A possible extension method extracting a `Bitmap`'s image data to CHW layout could be:

        /// <summary>
        /// Extracts image pixels in CHW
        /// </summary>
        /// <param name="image">The bitmap image to extract features from</param>
        /// <returns>A list of pixels in HWC order</returns>
        public static List<float> ExtractCHW(this Bitmap image)
        {
            var features = new List<float>(image.Width * image.Height * 3);
            for (int c = 0; c < 3; c++)
            {
                for (int h = 0; h < image.Height; h++)
                {
                    for (int w = 0; w < image.Width; w++)
                    {
                        var pixel = image.GetPixel(w, h);
                        float v = c == 0 ? pixel.B : c == 1 ? pixel.G : pixel.R;

                        features.Add(v);
                    }
                }
            }

            return features;
        }

The `Bitmap.GetPixel` method takes care of some of the memory layout nuances, allowing us to focus on the transformation itself.

This method can now be used in our image transformation prior to evaluation. Assuming the same bitmap as in Resize, we could extract its data in CHW layout with the following invocation:

    var features = testBitmap.ExtractCHW();

The `features` vector can now be used as a layer input into the image classification model. A working example is provided as part of the `CSEvalClient` example program, in particular refer to the `EvaluateImageClassificationModel` method.
