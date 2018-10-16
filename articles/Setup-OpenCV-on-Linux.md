---
title:   Setup OpenCV on Linux
author:    thiagofc
ms.author:   thiagofc
ms.date:   02/20/2018
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   quickstart
ms.devlang:   python
---

# Setup OpenCV on Linux 

You need to install OpenCV for CNTK 2.3 and above, if you want to build any of the the following:
- CNTK Image Reader
- CNTK Image Writer - required to use Tensorboard's Image feature.

If you plan to use both CUDA and OpenCV, [install CUDA first](./Setup-GPU-Specific-Packages-Linux.md)

OpenCV can have many interfaces and options.
In this section we cover only parts necessary to build CNTK.
Read more about installing OpenCV [here](http://docs.opencv.org/3.1.0/d7/d9f/tutorial_linux_install.html) and more generally [here](http://docs.opencv.org/3.1.0/df/d65/tutorial_table_of_content_introduction.html).

> [!IMPORTANT]
> Install OpenCV using the exact version and target path as specified below. This is necessary because it is expected by the CNTK build configuration program.

* Install OpenCV prerequisites. See [OpenCV installation instructions](http://docs.opencv.org/3.1.0/d7/d9f/tutorial_linux_install.html) for the full package list and explanations. See your platform documentation on how to install the packages. 

Example: for Ubuntu use the following command:

```
sudo apt-get install cmake libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
```

* Get OpenCV and install it. Use the following commands:

```
wget https://github.com/Itseez/opencv/archive/3.1.0.zip
unzip 3.1.0.zip
cd opencv-3.1.0
mkdir release
cd release
cmake -D WITH_CUDA=OFF -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local/opencv-3.1.0 ..
make all
sudo make install
```

Note that in the instructions above we suggest using "plain" `make` rather than `make -j`. We found that using `make -j` with OpenCV results in unstable system behavior and may result in a build failure and system crash.
