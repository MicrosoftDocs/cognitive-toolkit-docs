## Simple CNTK Installation

Microsoft Cognitive Toolkit (CNTK) is easy to install.

On Windows, if you have configured Anaconda3 Python 3.5 as your active Python environment, run this command to install the CNTK CPU-only version into your Python root environment:
```
pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0rc1-cp35-cp35m-win_amd64.whl
```
On Ubuntu 16.04, if you have configured Anaconda3 Python 3.5 as your active Python environment, run these commands to install the CNTK CPU-only version into your Python root environment:
```
sudo apt-get install openmpi-bin
pip install https://cntk.ai/PythonWheel/CPU-Only/cntk-2.0rc1-cp35-cp35m-linux_x86_64.whl
```
You can easily fine tune your PIP installation, including information about configuring your environment and the installation of tutorials, samples and Jupyter notebooks.
For Windows, check out [this page](./Setup-Windows-Python), for Linux [this one](./Setup-Linux-Python).

For the complete lists of CNTK installation options, see the table below.

### Install CNTK for the first time or update to a new version
-------------------------------
The Microsoft Cognitive Toolkit (CNTK) supports both 64-bit Windows and 64-bit Linux platforms.

You can install the complete source code of CNTK and build the binaries on your machine, but we 
also provide regular binary drops of the CNTK executables, including sample data and sample models.

#### Binary Installation (or Update) of CNTK

If you just want to download and install the latest precompiled binaries to your machine, follow the instructions here:

|Windows                  | Linux                   |
|:------------------------|:------------------------|
|[Python-only installation](./Setup-Windows-Python)<br>Simple pip install<br>of CNTK lib for<br>use in Python| [Python-only installation](./Setup-Linux-Python))<br>Simple pip install<br>of CNTK lib for<br>use in Python |
|[Script-driven installation](./Setup-Windows-Binary-Script)<br>Script that installs<br>CNTK Python lib and<br>CNTK exe for BrainScript | [Script-driven installation](./Setup-Linux-Binary-Script)<br>Script that installs<br>CNTK Python lib and<br>CNTK exe for BrainScript 
|[Manual installation](./Setup-Windows-Binary-Manual)<br>Manually install<br>CNTK Python lib,<br>CNTK exe for BrainScript,<br>and dependencies  | [Manual installation](./Setup-Linux-Binary-Manual)<br>Manually install<br>CNTK Python lib,<br>CNTK exe for BrainScript,<br>and dependencies
|                                                     | [Docker installation](./CNTK-Docker-Containers)
#### Installation and building of the CNTK codebase

If you want to take a look at the CNTK source code, compile CNTK yourself, make changes to the CNTK codebase, and contribute these changes back to the community, these are the pages for you:

|Windows                  | Linux                   |
|:------------------------|:------------------------|
|[Script-driven development setup](./Setup-CNTK-with-script-on-Windows) |
|[Manual development Setup](./Setup-CNTK-on-Windows) | [Manual Development Environment Setup](./Setup-CNTK-on-Linux) 
|[Migration from VS13 to VS15](./Setup-Migrate-VS13-to-VS15) | 
 

* [[Enabling 1bit SGD]]
* [[Developing and Testing]]
* [CNTK Production Test Configurations](./Test-Configurations)

#### Installation as Azure Virtual Machine or Linux Docker container

You may use CNTK via Microsoft Azure Virtual Machine offering (Windows and Linux) or install it as a Docker container (Linux). See the corresponding sections:

* [[CNTK on Azure]]
* [[CNTK Docker Containers]]

#### Usage and Samples

If you want to learn more about CNTK usage and how to execute the provided samples, you find more information on the following pages

* [[CNTK usage overview]]
* [[Examples]]
