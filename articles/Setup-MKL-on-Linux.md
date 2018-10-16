---
title:   Setup MKL on Linux
author:    thiagofc
ms.author:   thiagofc
ms.date:   02/20/2018
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   quickstart
ms.devlang:   python
---

# Setup MKL on Linux

The default CNTK math library is the [Intel Math Kernel Library (Intel MKL)](https://software.intel.com/en-us/intel-mkl/).
CNTK supports using the Intel MKL via a custom library version [MKLML](https://github.com/01org/mkl-dnn/releases), as well as MKL-DNN in this repo

Installing the MKL-DNN and MKLML library: 

* Create a directory on your machine to hold MKLML, e.g.:

```
sudo mkdir /usr/local/mklml
```

* Download the required MKLML v0.14 from the [MKLML web site](https://github.com/01org/mkl-dnn/releases).
  Un-tar it into your MKLML path, creating a versioned sub directory within.
  Build MKL-DNN using MKLML and install mkl-dnn to /usr/local

```
sudo wget https://github.com/01org/mkl-dnn/releases/download/v0.14/mklml_lnx_2018.0.3.20180406.tgz
sudo tar -xzf mklml_lnx_2018.0.3.20180406.tgz -C /usr/local/mklml
wget --no-verbose -O - https://github.com/01org/mkl-dnn/archive/v0.14.tar.gz | tar -xzf - && \
cd mkl-dnn-0.14 && \
ln -s /usr/local external && \
mkdir -p build && \
cd build && \
cmake .. && \
make && \
make install && \
cd ../.. && \
rm -rf mkl-dnn-0.14
```

  Note: if you want to build with different MKLML versions,

* When configuring the build (cf. below), specify the option `--with-mkl=<directory>`, e.g., `--with-mkl=/usr/local/mkl/mklml_lnx_2018.0.3.20180406`.
