Starting from CNTK **v.2.0 RC 1** we introduce new naming format for CNTK Shared Libraries for both Windows (`*.dll`, `*.lib`) and Linux (`*.so`) versions.

We also changed the names of the existing CNTK libraries to make them more meaningful and clearly distinguish between debug and release versions. Debug versions are now marked by letter `d` at the end of the file name (right before file extension). See complete name match tables below.

Finally we introduce the concept of **CNTK Component version** which is now an integral part of every name of CNTK Shared Libraries.

The new format of a CNTK Shared Library file name is:
```
<Library_name>-<CNTK_Component_Version>[d].<File_Extension>
```
See complete name match tables below.

#### Match between old and new CNTK Shared Libraries names. Windows.
In the table below we use `2.0rc1` as CNTK Component Version. **Note, that in your actual installation Component Version may, and very likely will be different.**

| Old name | New name Release | New name Debug | 
|:--------:|:----------------:|:--------------:|
|`ActionsLib.lib`|`Cntk.Actions-2.0rc1.lib`|`Cntk.Actions-2.0rc1d.lib`|
|`BinaryReader.dll`|` Cntk.Reader.Binary.Deprecated-2.0rc1.dll`| `Cntk.Reader.Binary.Deprecated-2.0rc1d.dll`|
|`BinaryReader.lib`|` Cntk.Reader.Binary.Deprecated-2.0rc1.lib`|` Cntk.Reader.Binary.Deprecated-2.0rc1d.lib`|
|`CNTK.lib`|`CNTK.lib` (no change)|`CNTK.lib` (no change)|
|`CNTKBinaryReader.dll`|`Cntk.Deserializers.Binary-2.0rc1.dll`|`Cntk.Deserializers.Binary-2.0rc1d.dll`|
|`CNTKBinaryReader.lib`|`Cntk.Deserializers.Binary-2.0rc1.lib`|`Cntk.Deserializers.Binary-2.0rc1d.lib`|
|`CNTKLibrary-2.0.dll`|`Cntk.Core-2.0rc1.dll`|`Cntk.Core-2.0rc1d.dll`|
|`CNTKLibrary-2.0.lib`|`Cntk.Core-2.0rc1.lib`|`Cntk.Core-2.0rc1d.lib`|
|`CNTKLibraryCSBinding.dll`|`Cntk.Core.CSBinding-2.0rc1.dll`|`Cntk.Core.CSBinding-2.0rc1d.dll`|
|`CNTKLibraryCSBinding.lib`|`Cntk.Core.CSBinding-2.0rc1.lib`|`Cntk.Core.CSBinding-2.0rc1d.lib`|
|`CNTKLibraryManaged-2.0.dll`|`Cntk.Core.Managed-2.0rc1.dll`|`Cntk.Core.Managed-2.0rc1d.dll`|
|`CNTKTextFormatReader.dll`|`Cntk.Deserializers.TextFormat-2.0rc1.dll`|`Cntk.Deserializers.TextFormat-2.0rc1d.dll`|
|`CNTKTextFormatReader.lib`|`Cntk.Deserializers.TextFormat-2.0rc1.lib`|`Cntk.Deserializers.TextFormat-2.0rc1d.lib`|
|`Common.lib`|`Cntk.Common-2.0rc1.lib`|`Cntk.Common-2.0rc1d.lib`|
|`CompositeDataReader.dll`|`Cntk.Composite-2.0rc1.dll`|`Cntk.Composite-2.0rc1d.dll`|
|`CompositeDataReader.lib`|`Cntk.Composite-2.0rc1.lib`|`Cntk.Composite-2.0rc1d.lib`|
|`DSSMReader.dll`|`Cntk.Reader.DSSM-2.0rc1.dll`|`Cntk.Reader.DSSM-2.0rc1d.dll`|
|`DSSMReader.lib`|`Cntk.Reader.DSSM-2.0rc1.lib`|`Cntk.Reader.DSSM-2.0rc1d.lib`|
|`EvalDll.dll`|`Cntk.Eval-2.0rc1.dll`|`Cntk.Eval-2.0rc1d.dll`|
|`EvalDll.lib`|`Cntk.Eval-2.0rc1.lib`|`Cntk.Eval-2.0rc1d.lib`|
|`EvalWrapper.dll`|`Cntk.Eval.Wrapper-2.0rc1.dll`|`Cntk.Eval.Wrapper-2.0rc1d.dll`|
|`HTKDeserializers.dll`|`Cntk.Deserializers.HTK-2.0rc1.dll`|`Cntk.Deserializers.HTK-2.0rc1d.dll`|
|`HTKDeserializers.lib`|`Cntk.Deserializers.HTK-2.0rc1.lib`|`Cntk.Deserializers.HTK-2.0rc1d.lib`|
|`HTKMLFReader.dll`|`Cntk.Reader.HTKMLF-2.0rc1.dll`|`Cntk.Reader.HTKMLF-2.0rc1d.dll`|
|`HTKMLFReader.lib`|`Cntk.Reader.HTKMLF-2.0rc1.lib`|`Cntk.Reader.HTKMLF-2.0rc1d.lib`|
|`ImageReader.dll`|`Cntk.Deserializers.Image-2.0rc1.dll`|`Cntk.Deserializers.Image-2.0rc1d.dll`|
|`ImageReader.lib`|`Cntk.Deserializers.Image-2.0rc1.lib`|`Cntk.Deserializers.Image-2.0rc1d.lib`|
|`LibSVMBinaryReader.dll`|`Cntk.Reader.SVMBinary-2.0rc1.dll`|`Cntk.Reader.SVMBinary-2.0rc1d.dll`|
|`LibSVMBinaryReader.lib`|`Cntk.Reader.SVMBinary-2.0rc1.lib`|`Cntk.Reader.SVMBinary-2.0rc1d.lib`|
|`LMSequenceReader.dll`|`Cntk.Reader.LMSequence-2.0rc1.dll`|`Cntk.Reader.LMSequence-2.0rc1d.dll`|
|`LMSequenceReader.lib`|`Cntk.Reader.LMSequence-2.0rc1.lib`|`Cntk.Reader.LMSequence-2.0rc1d.lib`|
|`LUSequenceReader.dll`|`Cntk.Reader.LUSequence-2.0rc1.dll`|`Cntk.Reader.LUSequence-2.0rc1d.dll`|
|`LUSequenceReader.lib`|`Cntk.Reader.LUSequence-2.0rc1.lib`|`Cntk.Reader.LUSequence-2.0rc1d.lib`|
|`Math.dll`|`Cntk.Math-2.0rc1.dll`|`Cntk.Math-2.0rc1d.dll`|
|`Math.lib`|`Cntk.Math-2.0rc1.lib`|`Cntk.Math-2.0rc1d.lib`|
|`MathCUDA.lib`|`Cntk.Math.Cuda-2.0rc1.lib`|`Cntk.Math.Cuda-2.0rc1d.lib`|
|`PerformanceProfilerDll.dll`|`Cntk.PerformanceProfiler-2.0rc1.dll`|`Cntk.PerformanceProfiler-2.0rc1d.dll`|
|`PerformanceProfilerDll.lib`|`Cntk.PerformanceProfiler-2.0rc1.lib`|`Cntk.PerformanceProfiler-2.0rc1d.lib`|
|`ReaderLib.lib`|`Cntk.Reader-2.0rc1.lib`|`Cntk.Reader-2.0rc1d.lib`|
|`SequenceTrainingLib.lib`|`Cntk.SequenceTrainingLib-2.0rc1.lib`|`Cntk.SequenceTrainingLib-2.0rc1d.lib`|
|`SGDLib.lib`|`Cntk.SGD-2.0rc1.lib`|`Cntk.SGD-2.0rc1d.lib`|
|`SparsePCReader.dll`|`Cntk.Reader.SparsePC-2.0rc1.dll`|`Cntk.Reader.SparsePC-2.0rc1d.dll`|
|`SparsePCReader.lib`|`Cntk.Reader.SparsePC-2.0rc1.lib`|`Cntk.Reader.SparsePC-2.0rc1d.lib`|
|`UCIFastReader.dll`|`Cntk.Reader.UCIFast-2.0rc1.dll`|`Cntk.Reader.UCIFast-2.0rc1d.dll`|
|`UCIFastReader.lib`|`Cntk.Reader.UCIFast-2.0rc1.lib`|`Cntk.Reader.UCIFast-2.0rc1d.lib`|

----------

#### Match between old and new CNTK Shared Libraries names. Linux.
In the table below we use `2.0rc1` as CNTK Component Version. **Note, that in your actual installation Component Version may, and very likely will be different.**

| Old name | New name Release | New name Debug | 
|:--------:|:----------------:|:--------------:|
|`CNTKBinaryReader.so`|`Cntk.Deserializers.Binary-2.0rc1.so`|`Cntk.Deserializers.Binary-2.0rc1d.so`|
|`CNTKTextFormatReader.so`|`Cntk.Deserializers.TextFormat-2.0rc1.so`|`Cntk.Deserializers.TextFormat-2.0rc1d.so`|
|`CompositeDataReader.so`|`Cntk.Composite-2.0rc1.so`|`Cntk.Composite-2.0rc1d.so`|
|`HTKDeserializers.so`|`Cntk.Deserializers.HTK-2.0rc1.so`|`Cntk.Deserializers.HTK-2.0rc1d.so`|
|`HTKMLFReader.so`|`Cntk.Reader.HTKMLF-2.0rc1.so`|`Cntk.Reader.HTKMLF-2.0rc1d.so`|
|`ImageReader.so`|`Cntk.Deserializers.Image-2.0rc1.so`|`Cntk.Deserializers.Image-2.0rc1d.so`|
|`Kaldi2Reader.so`|`Cntk.Reader.Kaldi2-2.0rc1.so`|`Cntk.Reader.Kaldi2-2.0rc1d.so`|
|`libcntklibrary-2.0.so`|`libCntk.Core-2.0rc1.so`|`libCntk.Core-2.0rc1d.so`|
|`libcntkmath.so`|`libCntk.Math-2.0rc1.so`|`libCntk.Math-2.0rc1d.so`|
|`libeval.so`|`libCntk.Eval-2.0rc1.so`|`libCntk.Eval-2.0rc1d.so`|
|`libperfprofiler.so`|`libCntk.PerformanceProfiler-2.0rc1.so`|`libCntk.PerformanceProfiler-2.0rc1d.so`|
|`LibSVMBinaryReader.so`|`Cntk.Reader.SVMBinary-2.0rc1.so`|`Cntk.Reader.SVMBinary-2.0rc1d.so`|
|`LMSequenceReader.so`|`Cntk.Reader.LMSequence-2.0rc1.so`|`Cntk.Reader.LMSequence-2.0rc1d.so`|
|`LUSequenceReader.so`|`Cntk.Reader.LUSequence-2.0rc1.so`|`Cntk.Reader.LUSequence-2.0rc1d.so`|
|`SparsePCReader.so`|`Cntk.Reader.SparsePC-2.0rc1.so`|`Cntk.Reader.SparsePC-2.0rc1d.so`|
|`UCIFastReader.so`|`Cntk.Reader.UCIFast-2.0rc1.so`|`Cntk.Reader.UCIFast-2.0rc1d.so`|





  
