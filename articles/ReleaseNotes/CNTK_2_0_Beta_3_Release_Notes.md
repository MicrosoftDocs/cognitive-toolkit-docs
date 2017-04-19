## CNTK v.2.0 Beta 3 Release Notes

This is a summary of new features delivered with the Beta 3 release of CNTK V.2.0.

### Integration with NVIDIA NCCL

This Release introduces the integration of CNTK and [NVIDIA NCCL](https://github.com/NVIDIA/nccl), a stand-alone library of standard collective communication routines, such as all-gather, reduce, broadcast, etc., that have been optimized to achieve high bandwidth over PCIe. 

NCCL library supports Linux system only. NCCL can be enabled for those who build CNTK from the source code. See how to enable NCCL in the [CNTK Wiki](https://github.com/Microsoft/CNTK/wiki/Setup-CNTK-on-Linux#optional-nccl).

### CNTK Evaluation library. NuGet package

This Release features NuGet package for CNTK Evaluation library. **IMPORTANT!** In Visual Studio *Manage Nuget Packages* Window change the default option *Stable Only* to *Include Prerelease*. Otherwise the package will not be visible. The Package version should be ```2.0-beta3```.

### Stability Improvements
We continue fine tuning new features and fixing different bugs - thank you once again for the constant feedback. You are not required to adopt your code or models to take an advantage of these improvements.