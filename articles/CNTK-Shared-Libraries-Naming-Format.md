---
title:   CNTK Shared Libraries Naming Format
author:    chrisbasoglu
ms.author:   cbasoglu
ms.date:   06/01/2017
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.service:  Cognitive-services
ms.devlang:   NA
---

# CNTK Shared Libraries Naming Format

CNTK uses a common naming scheme for CNTK Shared Libraries on Windows (`*.dll`, `*.lib`) and Linux (`*.so`). The name of a file consists out of the library name, the CNTK component version, and the letter `d` at the end of the file name (right before file extension). See the complete name match tables below.

The format of a CNTK Shared Library file name is:
```
<Library_name>-<CNTK_Component_Version>[d].<File_Extension>
```

#### Binary file names on Windows
In the table below we use `2.0` as CNTK Component Version.

| Name Release | Name Debug | 
|:----------------:|:--------------:|
|`Cntk.Actions-2.0.lib`|`Cntk.Actions-2.0d.lib`|
|`Cntk.Reader.Binary.Deprecated-2.0.dll`| `Cntk.Reader.Binary.Deprecated-2.0d.dll`|
|`Cntk.Reader.Binary.Deprecated-2.0.lib`| `Cntk.Reader.Binary.Deprecated-2.0d.lib`|
|`CNTK.lib` |`CNTK.lib` |
|`Cntk.Deserializers.Binary-2.0.dll`|`Cntk.Deserializers.Binary-2.0d.dll`|
|`Cntk.Deserializers.Binary-2.0.lib`|`Cntk.Deserializers.Binary-2.0d.lib`|
|`Cntk.Core-2.0.dll`|`Cntk.Core-2.0d.dll`|
|`Cntk.Core-2.0.lib`|`Cntk.Core-2.0d.lib`|
|`Cntk.Core.CSBinding-2.0.dll`|`Cntk.Core.CSBinding-2.0d.dll`|
|`Cntk.Core.CSBinding-2.0.lib`|`Cntk.Core.CSBinding-2.0d.lib`|
|`Cntk.Core.JavaBinding-2.0.dll`|`Cntk.Core.JavaBinding-2.0d.dll`|
|`Cntk.Core.JavaBinding-2.0.lib`|`Cntk.Core.JavaBinding-2.0d.lib`|
|`Cntk.Core.Managed-2.0.dll`|`Cntk.Core.Managed-2.0d.dll`|
|`Cntk.Deserializers.TextFormat-2.0.dll`|`Cntk.Deserializers.TextFormat-2.0d.dll`|
|`Cntk.Deserializers.TextFormat-2.0.lib`|`Cntk.Deserializers.TextFormat-2.0d.lib`|
|`Cntk.Common-2.0.lib`|`Cntk.Common-2.0d.lib`|
|`Cntk.Composite-2.0.dll`|`Cntk.Composite-2.0d.dll`|
|`Cntk.Composite-2.0.lib`|`Cntk.Composite-2.0d.lib`|
|`Cntk.Reader.DSSM-2.0.dll`|`Cntk.Reader.DSSM-2.0d.dll`|
|`Cntk.Reader.DSSM-2.0.lib`|`Cntk.Reader.DSSM-2.0d.lib`|
|`Cntk.Eval-2.0.dll`|`Cntk.Eval-2.0d.dll`|
|`Cntk.Eval-2.0.lib`|`Cntk.Eval-2.0d.lib`|
|`Cntk.Eval.Wrapper-2.0.dll`|`Cntk.Eval.Wrapper-2.0d.dll`|
|`Cntk.Deserializers.HTK-2.0.dll`|`Cntk.Deserializers.HTK-2.0d.dll`|
|`Cntk.Deserializers.HTK-2.0.lib`|`Cntk.Deserializers.HTK-2.0d.lib`|
|`Cntk.Reader.HTKMLF-2.0.dll`|`Cntk.Reader.HTKMLF-2.0d.dll`|
|`Cntk.Reader.HTKMLF-2.0.lib`|`Cntk.Reader.HTKMLF-2.0d.lib`|
|`Cntk.Deserializers.Image-2.0.dll`|`Cntk.Deserializers.Image-2.0d.dll`|
|`Cntk.Deserializers.Image-2.0.lib`|`Cntk.Deserializers.Image-2.0d.lib`|
|`Cntk.Reader.SVMBinary-2.0.dll`|`Cntk.Reader.SVMBinary-2.0d.dll`|
|`Cntk.Reader.SVMBinary-2.0.lib`|`Cntk.Reader.SVMBinary-2.0d.lib`|
|`Cntk.Reader.LMSequence-2.0.dll`|`Cntk.Reader.LMSequence-2.0d.dll`|
|`Cntk.Reader.LMSequence-2.0.lib`|`Cntk.Reader.LMSequence-2.0d.lib`|
|`Cntk.Reader.LUSequence-2.0.dll`|`Cntk.Reader.LUSequence-2.0d.dll`|
|`Cntk.Reader.LUSequence-2.0.lib`|`Cntk.Reader.LUSequence-2.0d.lib`|
|`Cntk.Math-2.0.dll`|`Cntk.Math-2.0d.dll`|
|`Cntk.Math-2.0.lib`|`Cntk.Math-2.0d.lib`|
|`Cntk.Math.Cuda-2.0.lib`|`Cntk.Math.Cuda-2.0d.lib`|
|`Cntk.PerformanceProfiler-2.0.dll`|`Cntk.PerformanceProfiler-2.0d.dll`|
|`Cntk.PerformanceProfiler-2.0.lib`|`Cntk.PerformanceProfiler-2.0d.lib`|
|`Cntk.Reader-2.0.lib`|`Cntk.Reader-2.0d.lib`|
|`Cntk.SequenceTrainingLib-2.0.lib`|`Cntk.SequenceTrainingLib-2.0d.lib`|
|`Cntk.SGD-2.0.lib`|`Cntk.SGD-2.0d.lib`|
|`Cntk.Reader.SparsePC-2.0.dll`|`Cntk.Reader.SparsePC-2.0d.dll`|
|`Cntk.Reader.SparsePC-2.0.lib`|`Cntk.Reader.SparsePC-2.0d.lib`|
|`Cntk.Reader.UCIFast-2.0.dll`|`Cntk.Reader.UCIFast-2.0d.dll`|
|`Cntk.Reader.UCIFast-2.0.lib`|`Cntk.Reader.UCIFast-2.0d.lib`|

----------

#### Binary file names on Linux
In the table below we use `2.0` as CNTK Component Version.

| Name Release | Name Debug | 
|:----------------:|:--------------:|
|`Cntk.Deserializers.Binary-2.0.so`|`Cntk.Deserializers.Binary-2.0d.so`|
|`Cntk.Deserializers.TextFormat-2.0.so`|`Cntk.Deserializers.TextFormat-2.0d.so`|
|`Cntk.Composite-2.0.so`|`Cntk.Composite-2.0d.so`|
|`Cntk.Deserializers.HTK-2.0.so`|`Cntk.Deserializers.HTK-2.0d.so`|
|`Cntk.Reader.HTKMLF-2.0.so`|`Cntk.Reader.HTKMLF-2.0d.so`|
|`Cntk.Deserializers.Image-2.0.so`|`Cntk.Deserializers.Image-2.0d.so`|
|`Cntk.Reader.Kaldi2-2.0.so`|`Cntk.Reader.Kaldi2-2.0d.so`|
|`libCntk.Core-2.0.so`|`libCntk.Core-2.0d.so`|
|`libCntk.Core.JavaBinding-2.0.so`|`libCntk.Core.JavaBinding-2.0d.so`|
|`libCntk.Math-2.0.so`|`libCntk.Math-2.0d.so`|
|`libCntk.Eval-2.0.so`|`libCntk.Eval-2.0d.so`|
|`libCntk.PerformanceProfiler-2.0.so`|`libCntk.PerformanceProfiler-2.0d.so`|
|`Cntk.Reader.SVMBinary-2.0.so`|`Cntk.Reader.SVMBinary-2.0d.so`|
|`Cntk.Reader.LMSequence-2.0.so`|`Cntk.Reader.LMSequence-2.0d.so`|
|`Cntk.Reader.LUSequence-2.0.so`|`Cntk.Reader.LUSequence-2.0d.so`|
|`Cntk.Reader.SparsePC-2.0.so`|`Cntk.Reader.SparsePC-2.0d.so`|
|`Cntk.Reader.UCIFast-2.0.so`|`Cntk.Reader.UCIFast-2.0d.so`|
