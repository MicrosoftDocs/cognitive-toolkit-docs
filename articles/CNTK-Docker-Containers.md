---
title:   CNTK Docker Containers
author:    alexeyo26
ms.author:   alexeyo
ms.date:   07/31/2017
ms.prod: cntk
ms.custom:   cognitive-toolkit
ms.topic:   conceptual
ms.devlang:   NA
---

# CNTK Docker Containers

You can set up CNTK as a [Docker Container](https://en.wikipedia.org/wiki/Docker_(software)) on your Linux system.

There are two ways of using CNTK Docker Containers:

- [CNTK Docker Containers](#cntk-docker-containers)
  - [Using CNTK Images published at Docker Hub](#using-cntk-images-published-at-docker-hub)
    - [Using Docker container to run CNTK Jupyter Notebook tutorials](#using-docker-container-to-run-cntk-jupyter-notebook-tutorials)
  - [Building CNTK Docker Images](#building-cntk-docker-images)

## Using CNTK Images published at Docker Hub

We host public CNTK Images at Docker Hub. See complete list of Images available at [CNTK Repositories page at Docker Hub](https://hub.docker.com/_/microsoft-cntk). We currently host only **runtime** configurations. *Runtime* configuration corresponds to an environment with [CNTK Binary package installed and configured](./Setup-CNTK-on-your-machine.md). This configuration contains neither CNTK source code, nor the prerequisites required to build CNTK.

Note, that you need [NVIDIA Docker](https://github.com/nvidia/nvidia-docker) to use CNTK GPU-enabled images.

Standard Docker commands are used to get the image:
```
docker pull mcr.microsoft.com/cntk/release
```
to get the latest official release image, which today means latest available GPU runtime configuration. You also can get the latest development nightly image:
```
docker pull mcr.microsoft.com/cntk/nightly
```

To get a specific configuration you need to add a tag. E.g.
```
docker pull mcr.microsoft.com/cntk/release:2.7-cpu-python3.5
```
will get you CNTK 2.7 CPU runtime configuration set up for Python 3.5.

If you are unfamiliar with Docker, read [sections below](#building-cntk-docker-images) at this page.

### Using Docker container to run CNTK Jupyter Notebook tutorials

You can use CNTK Docker containers to run CNTK Jupyter Notebooks in your local environment.

We assume that you have already pulled the required images from Docker Hub. In the example below we will use GPU configuration. If you are using CPU configuration, then in the commands below replace all occurrences of `nvidia-docker` with `docker`.

First create and start a CNTK container in detached mode with IP port exposed (we use port `8888` which is default for Jupyter Notebook application):
```
nvidia-docker run -d -p 8888:8888 --name cntk-jupyter-notebooks -t mcr.microsoft.com/cntk/release
```

Now start Jupyter Notebook server in your Docker container:
```
docker exec -it cntk-jupyter-notebooks bash -c "source /cntk/activate-cntk && jupyter-notebook --no-browser --port=8888 --ip=0.0.0.0 --notebook-dir=/cntk/Tutorials --allow-root"
```

In your terminal you will see the console output of Jupyter Notebooks server. This output would contain a line like this:  
`http://0.0.0.0:8888/?token=082684fbe2b43eebd72583c301c05072084173d0ac06a4d7`

Copy the token displayed (in our example `082684fbe2b43eebd72583c301c05072084173d0ac06a4d7`).

Now you may access CNTK Jupyter Notebooks using the IP address of the machine where you are running the Docker container. I.e. if your machine address is `192.168.1.1` then to access CNTK Notebooks open a browser window and go to
`http://192.168.1.1:8888`.

Note, that during the first run Jupyter Notebook application will ask for a password or token. Use the token you have saved above.

To stop the Jupyter Notebook server send `Ctrl-C` sequence in the terminal where you have Jupiter Notebook server console output and confirm shutting down the server. Note that it will **not** stop the Docker container itself. To stop the container use the command:  
`docker stop cntk-jupyter-notebooks`

**WARNING!** The commands above will expose Jupyter Notebooks application to **everybody** who can access the IP address of the machine where you are running the Docker container. 

## Building CNTK Docker Images

You can build and run CNTK using the same container and this is a recommended approach to reproduce our reference configuration.

First you need to install docker. It is highly recommended to follow the [installation process in the official docker documentation](https://docs.docker.com/engine/installation/linux/ubuntulinux/). Versions that come with your Linux distribution might be outdated and will not work with `nvidia-docker` (which you may need to install in addition to docker if you plan to build and run the GPU image from within the same container). You should also follow the instructions in the optional section titled [creating a docker group](https://docs.docker.com/engine/installation/linux/ubuntulinux/#/create-a-docker-group).

The correspondent Docker files are in the CNTK Repository at https://github.com/Microsoft/CNTK/tree/release/latest/Tools/docker

To build a docker image with CNTK and all its dependencies, simply clone the CNTK repository, navigate to `CNTK/Tools/docker` and use the Dockerfile you want to build from (CPU or GPU). For example, to build CNTK's GPU docker image, execute:
```
docker build -t cntk -f CNTK-GPU-Image/Dockerfile .
```
The `-f <path/to/Dockerfile>` argument is required because some patches, common for both CPU and GPU dockerfiles, need to be applied on SWIG source code.
If you receive errors that say `Could not resolve 'archive.ubuntu.com'`
you will need to provide docker with the IP addresses of your DNS servers. 
First find the IP addresses of your DNS servers using, for example, the command
```
nm-tool
```
or the command
```
nmcli dev show
```
Let's say that the IPs of your DNS servers are `a.b.c.d` and `x.y.z.w`.
Then 
* on Ubuntu 15.10 and later (or other Linux that uses systemd)
  modify `/lib/systemd/system/docker.service` so that the docker 
  daemon is started with the additional options 
  `--dns a.b.c.d --dns x.y.z.w`
* on Ubuntu 15.04 and earlier (or other Linux that does not use systemd)
  edit `/etc/default/docker` so that the line
  ```
  #DOCKER_OPTS="--dns 8.8.8.8 --dns 8.8.4.4"
  ```
  is uncommented and contains the IP addresses of your DNS servers.

Note: some companies block public DNS servers such as 8.8.8.8
and 8.8.4.4. You can try using them but if the problem persists
you should try to use the DNS server IP addresses reported by `nm-tool/nmcli`.

Restart the docker daemon via 
```
sudo service docker restart
```
and delete any docker images that where created with the wrong DNS settings.
To delete all docker images do
```
docker rmi $(docker images -q)
```
To delete all docker containers do
```
docker rm $(docker ps -a -q)
```
Now try again 
```
docker build -t cntk -f CNTK-GPU-Image/Dockerfile .
```
If you have a GPU you'll want to test if you can access it through a docker container once you have built the image. Try this command:
```
docker run --rm cntk nvidia-smi
```
If it works, you are done. If it doesn't, it means that there is a mismatch between the CUDA version and/or drivers installed on your host and in your CNTK docker image. In particular, the mismatch is between the kernel-mode NVidia driver module and the user-mode module (which is a shared lib) and this happens if the version on the host does not exactly match the version in the container. Fortunately this is easy to fix. Just install [nvidia-docker](https://github.com/NVIDIA/nvidia-docker) and use it exactly like docker (no need to rebuild the image).
```
nvidia-docker run --rm cntk nvidia-smi
```
This should work and enables CNTK to use the GPU from inside a docker container. If this does not work, search the Issues section on the [nvidia-docker GitHub](https://github.com/NVIDIA/nvidia-docker/issues) -- many solutions are already documented. Note that if your /usr and /var directories are in different partitions, you will need some extra steps like [here](https://github.com/NVIDIA/nvidia-docker/issues/211). To get an interactive shell to a container that will *not* be automatically deleted after you exit do
```
nvidia-docker run --name cntk_container1 -ti cntk bash
```
If you want to share your data and configurations between the host (your machine or VM) and the container in which you are using CNTK, use the -v option, e.g.
```
nvidia-docker run --name cntk_container1 -ti -v /project1/data:/data -v /project1/config:/config cntk bash
```
This will make /project1/data from the host visible as /data in the container, and /project1/config as /config. Such isolation reduces the chances of your containerized experiments overwriting or using wrong data.
