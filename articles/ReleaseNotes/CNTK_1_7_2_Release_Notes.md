## CNTK v.1.7.2 Release Notes

This is a summary on what's new in CNTK 1.7.2 Binary Release. 

**This is a Hot Fix Release. It affects all users of Model Evaluation Library**

If you are NOT using Model Evaluation Library you may skip this release.  
If you ARE using Model Evaluation Library we **strongly recommend** installing version 1.7.2 instead of **any** previous version you might be using.

### Detailed Description

We have identified a bug in **CNTK Model Evaluation Library**. In particular it related   to ```EvalExtended``` interface and could cause unexpected exceptions and crashes due to memory access violation.

The bug was present in **all** previous versions of CNTK Model Evaluation Library.

The bug mainly affects Windows users but we strongly recommend to install v.1.7.2 to users of **both Windows and Linux**.