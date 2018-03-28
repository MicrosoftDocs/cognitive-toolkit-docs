---
title:   Install CNTK for BrainScript
author:    mx-iao
ms.author:   minxia
ms.date:   03/27/2018
ms.custom:   cognitive-toolkit
ms.topic:   get-started-article
ms.devlang:   brainscript
---

# Install CNTK for BrainScript

## Setup for Windows

### Scripted Install
For a scripted binary installation of CNTK on Windows, follow Steps 1 and 2 on this [page](/cognitive-toolkit/Setup-Windows-Binary-Script).  

**Then, verify the setup for BrainScript:**

Perform the following command in the CNTK environment command prompt (see previous step):

```
cd c:\local\CNTK-2-5
cd Tutorials\HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

The last lines of the CNTK output on the console should look similar to this:

```
Finished Epoch[42 of 50]: [Training] lr = 0.04287672 * 1000; err = 0.01152817 * 1000; totalSamplesSeen = 42000; learningRatePerSample = 0.039999999; epochTime=0.050296s
Finished Epoch[43 of 50]: [Training] lr = 0.04388479 * 1000; err = 0.01206375 * 1000; totalSamplesSeen = 43000; learningRatePerSample = 0.039999999; epochTime=0.052143s
Finished Epoch[44 of 50]: [Training] lr = 0.04223433 * 1000; err = 0.01105073 * 1000; totalSamplesSeen = 44000; learningRatePerSample = 0.039999999; epochTime=0.057235s
Finished Epoch[45 of 50]: [Training] lr = 0.04208072 * 1000; err = 0.01140516 * 1000; totalSamplesSeen = 45000; learningRatePerSample = 0.039999999; epochTime=0.051414s
Finished Epoch[46 of 50]: [Training] lr = 0.04261674 * 1000; err = 0.01158323 * 1000; totalSamplesSeen = 46000; learningRatePerSample = 0.039999999; epochTime=0.051115s
Finished Epoch[47 of 50]: [Training] lr = 0.04326523 * 1000; err = 0.01164283 * 1000; totalSamplesSeen = 47000; learningRatePerSample = 0.039999999; epochTime=0.051611s
Finished Epoch[48 of 50]: [Training] lr = 0.04225255 * 1000; err = 0.01148774 * 1000; totalSamplesSeen = 48000; learningRatePerSample = 0.039999999; epochTime=0.0509s
Finished Epoch[49 of 50]: [Training] lr = 0.04173276 * 1000; err = 0.01124948 * 1000; totalSamplesSeen = 49000; learningRatePerSample = 0.039999999; epochTime=0.049659s
Finished Epoch[50 of 50]: [Training] lr = 0.04399402 * 1000; err = 0.01202178 * 1000; totalSamplesSeen = 50000; learningRatePerSample = 0.039999999; epochTime=0.052725s

COMPLETED.
```

If you have an NVidia GPU and installed a GPU build, you can also try this command:

```
cntk configFile=lr_bs.cntk makeMode=false command=Train deviceId=auto
```

To validate that the GPU was being used, look for the following line in your output:

```
Model has 9 nodes. Using GPU 0.
```

### Manual Install
To manually install CNTK from a binary distribution, follow Step 1 on this [page](/cognitive-toolkit/Setup-Windows-Binary-Manual).

**Then, verify the setup for BrainScript:**
Please add the CNTK binary directory (e.g., `c:\local\CNTK\cntk`) to your path.

To validate, please perform the following commands:
```
cd c:\local\CNTK\Tutorials\HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```
The last lines of the CNTK output on the console should look similar to this:
```
Finished Epoch[42 of 50]: [Training] lr = 0.04287672 * 1000; err = 0.01152817 * 1000; totalSamplesSeen = 42000; learningRatePerSample = 0.039999999; epochTime=0.050296s
Finished Epoch[43 of 50]: [Training] lr = 0.04388479 * 1000; err = 0.01206375 * 1000; totalSamplesSeen = 43000; learningRatePerSample = 0.039999999; epochTime=0.052143s
Finished Epoch[44 of 50]: [Training] lr = 0.04223433 * 1000; err = 0.01105073 * 1000; totalSamplesSeen = 44000; learningRatePerSample = 0.039999999; epochTime=0.057235s
Finished Epoch[45 of 50]: [Training] lr = 0.04208072 * 1000; err = 0.01140516 * 1000; totalSamplesSeen = 45000; learningRatePerSample = 0.039999999; epochTime=0.051414s
Finished Epoch[46 of 50]: [Training] lr = 0.04261674 * 1000; err = 0.01158323 * 1000; totalSamplesSeen = 46000; learningRatePerSample = 0.039999999; epochTime=0.051115s
Finished Epoch[47 of 50]: [Training] lr = 0.04326523 * 1000; err = 0.01164283 * 1000; totalSamplesSeen = 47000; learningRatePerSample = 0.039999999; epochTime=0.051611s
Finished Epoch[48 of 50]: [Training] lr = 0.04225255 * 1000; err = 0.01148774 * 1000; totalSamplesSeen = 48000; learningRatePerSample = 0.039999999; epochTime=0.0509s
Finished Epoch[49 of 50]: [Training] lr = 0.04173276 * 1000; err = 0.01124948 * 1000; totalSamplesSeen = 49000; learningRatePerSample = 0.039999999; epochTime=0.049659s
Finished Epoch[50 of 50]: [Training] lr = 0.04399402 * 1000; err = 0.01202178 * 1000; totalSamplesSeen = 50000; learningRatePerSample = 0.039999999; epochTime=0.052725s

COMPLETED.
```
If you have an NVidia GPU and installed a GPU build, you can also try this command:
```
cntk configFile=lr_bs.cntk makeMode=false command=Train deviceId=auto
```
To validate that the GPU was being used, look for the following line in your output:
```
Model has 9 nodes. Using GPU 0.
```

## Setup for Linux

### Scripted Install

For a scripted binary installation of CNTK on Windows, follow Steps 1 and 2 on this [page](/cognitive-toolkit/Setup-Linux-Binary-Script).

**Then, verify the setup for BrainScript**

Perform the following command in the CNTK environment command prompt (see previous step):
```
cd /home/username/cntk/Tutorials/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```
The last lines of the CNTK output on the console should look similar to this:
```
Finished Epoch[42 of 50]: [Training] lr = 0.04287672 * 1000; err = 0.01152817 * 1000; totalSamplesSeen = 42000; learningRatePerSample = 0.039999999; epochTime=0.050296s
Finished Epoch[43 of 50]: [Training] lr = 0.04388479 * 1000; err = 0.01206375 * 1000; totalSamplesSeen = 43000; learningRatePerSample = 0.039999999; epochTime=0.052143s
Finished Epoch[44 of 50]: [Training] lr = 0.04223433 * 1000; err = 0.01105073 * 1000; totalSamplesSeen = 44000; learningRatePerSample = 0.039999999; epochTime=0.057235s
Finished Epoch[45 of 50]: [Training] lr = 0.04208072 * 1000; err = 0.01140516 * 1000; totalSamplesSeen = 45000; learningRatePerSample = 0.039999999; epochTime=0.051414s
Finished Epoch[46 of 50]: [Training] lr = 0.04261674 * 1000; err = 0.01158323 * 1000; totalSamplesSeen = 46000; learningRatePerSample = 0.039999999; epochTime=0.051115s
Finished Epoch[47 of 50]: [Training] lr = 0.04326523 * 1000; err = 0.01164283 * 1000; totalSamplesSeen = 47000; learningRatePerSample = 0.039999999; epochTime=0.051611s
Finished Epoch[48 of 50]: [Training] lr = 0.04225255 * 1000; err = 0.01148774 * 1000; totalSamplesSeen = 48000; learningRatePerSample = 0.039999999; epochTime=0.0509s
Finished Epoch[49 of 50]: [Training] lr = 0.04173276 * 1000; err = 0.01124948 * 1000; totalSamplesSeen = 49000; learningRatePerSample = 0.039999999; epochTime=0.049659s
Finished Epoch[50 of 50]: [Training] lr = 0.04399402 * 1000; err = 0.01202178 * 1000; totalSamplesSeen = 50000; learningRatePerSample = 0.039999999; epochTime=0.052725s

COMPLETED.
```
If you have an NVidia GPU and installed a GPU build, you can also try this command:
```
cntk configFile=lr_bs.cntk makeMode=false command=Train deviceId=auto
```
To validate that the GPU was being used, look for the following line in your output:
```
Model has 9 nodes. Using GPU 0.
```

### Manual Install

To manually install CNTK from a binary distribution, follow Step 1 on this [page](/cognitive-toolkit/Setup-Linux-Binary-Manual).

**Then, verify the setup for BrainScript:**

* Set the following environment variables (we assume that the CNTK archive is extracted to ```/home/username/cntkbin```):

```
export PATH=/home/username/cntkbin/cntk/bin:$PATH
export LD_LIBRARY_PATH=/home/username/cntkbin/cntk/lib:/home/username/cntkbin/cntk/dependencies/lib:$LD_LIBRARY_PATH
```

Note: If you've built openmpi yourself (for example, on Ubuntu 14), make sure to add its library directory to `LD_LIBRARY_PATH` as well:

```
export LD_LIBRARY_PATH=[path-to-your-openmpi-installation]/lib:$LD_LIBRARY_PATH
```

To validate, please perform the following commands:

```
cd /home/username/cntkbin/Tutorials/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

The last lines of the CNTK output on the console should look similar to this:

```
Finished Epoch[42 of 50]: [Training] lr = 0.04287672 * 1000; err = 0.01152817 * 1000; totalSamplesSeen = 42000; learningRatePerSample = 0.039999999; epochTime=0.050296s
Finished Epoch[43 of 50]: [Training] lr = 0.04388479 * 1000; err = 0.01206375 * 1000; totalSamplesSeen = 43000; learningRatePerSample = 0.039999999; epochTime=0.052143s
Finished Epoch[44 of 50]: [Training] lr = 0.04223433 * 1000; err = 0.01105073 * 1000; totalSamplesSeen = 44000; learningRatePerSample = 0.039999999; epochTime=0.057235s
Finished Epoch[45 of 50]: [Training] lr = 0.04208072 * 1000; err = 0.01140516 * 1000; totalSamplesSeen = 45000; learningRatePerSample = 0.039999999; epochTime=0.051414s
Finished Epoch[46 of 50]: [Training] lr = 0.04261674 * 1000; err = 0.01158323 * 1000; totalSamplesSeen = 46000; learningRatePerSample = 0.039999999; epochTime=0.051115s
Finished Epoch[47 of 50]: [Training] lr = 0.04326523 * 1000; err = 0.01164283 * 1000; totalSamplesSeen = 47000; learningRatePerSample = 0.039999999; epochTime=0.051611s
Finished Epoch[48 of 50]: [Training] lr = 0.04225255 * 1000; err = 0.01148774 * 1000; totalSamplesSeen = 48000; learningRatePerSample = 0.039999999; epochTime=0.0509s
Finished Epoch[49 of 50]: [Training] lr = 0.04173276 * 1000; err = 0.01124948 * 1000; totalSamplesSeen = 49000; learningRatePerSample = 0.039999999; epochTime=0.049659s
Finished Epoch[50 of 50]: [Training] lr = 0.04399402 * 1000; err = 0.01202178 * 1000; totalSamplesSeen = 50000; learningRatePerSample = 0.039999999; epochTime=0.052725s

COMPLETED.
```

If you have an NVidia GPU and installed a GPU build, you can also try this command:

```
cntk configFile=lr_bs.cntk makeMode=false command=Train deviceId=auto
```

To validate that the GPU was being used, look for the following line in your output:

```
Model has 9 nodes. Using GPU 0.
```
