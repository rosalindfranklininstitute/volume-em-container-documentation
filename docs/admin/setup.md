# Administrator Guide to RFI Volume EM Containers

These are setup notes for system administrators, RSEs and other
technicals looking to provide the containerised software to their users. 

## Requirements
The main requirements are a working container runtime and display
server. Each software container expects user interaction via a GUI, although
IMOD also provides a CLI.  
Second, except for Fiji, we recommend a NVIDIA GPU with current drivers, CUDA, and (Docker) 
NVIDIA container runtime. 
Containers run without GPU but performance degrades significantly and some features may fail. If you do not have an NVIDIA GPU or do not wish to use it, omit the `--gpu`/`--nv` flags in the Apptainer/Docker commands below.

Each container has been tested using both Docker and Apptainer, and commands for
both platforms are provided below, although we recommend Apptainer for multi-user
environments and remote servers. Testing has been performed on Linux (Ubuntu
Desktop 24.04) and Windows via Windows Subsystem for Linux. We are looking into
extending tests to MacOS with X11 support from XQuartz or Xpra.

AMD GPU support is untested but may work with ROCm in 
[Docker](https://rocm.docs.amd.com/projects/install-on-linux/en/latest/how-to/docker.html)
and [Apptainer](https://apptainer.org/docs/user/main/gpu.html#amd-gpus-rocm).


## General Recommendations


The main configuration challenge is getting the container to
interface correctly with display server and GPU. For Docker, it is typically
necessary to authorize X server access with `xhost +local:` or `xhost +` (note this has [security
implications](https://wiki.archlinux.org/title/Xhost) for internet-connected
devices). 

Next, verify `$DISPLAY` is set correctly in the target session context (this 
is often a trouble point for remote connections served e.g. via RDP or VNC). 
For Docker, it is generally necessary to pass this variable into the environment of
the container, mount `/tmp/.X11-unix` and the `/dev/dri` device (see below).
No specific configuration should be required for Apptainer except setting
`$DISPLAY` for the MIB2 container below.

To work with user data, containers need access to the host filesystem.
Apptainer automatically provides access to common locations including `/home/$USER/`,
`/tmp` and the current working directory, and additional directories
can be mounted inside the container by adding `--bind
/path/on/host/:/path/in/container` arguments when running the container. 
Docker on the other hand requires explicit bind mounts for all host directories
using the `--volume /path/on/host:/path/in/container`.

Finally, we are looking to provide a set of quick tests that can be run
to verify the configuration of the containers on your system.
If you would like to help us here, please let us know. In the time being we
have linked out to the main documentation pages where an example usage
(with publicly available data) is given.



## Running the containers
Depending on user expertise, it may be sufficient to simply provide
the commands below. Otherwise, you may want to provide access to the commands via e.g., a
desktop shortcut or module share. In any case you should make sure appropriate
bind mounts are used to allow users access to their data.

With the exception of the IMOD container, running the containers below will
launch the software's main GUI directly. For IMOD, the main executables are
`etomo` and `3dmod` (GUI).

All containers use the `latest` tag by default. Pin to specific versions in production environments.

**N.B.** Dockerfiles for each container are available on public GitHub
repositories. Built images are available on the RFI quay repository. We provide
links for both sources below. The containers are distributed under 
Apache 2.0 or GPL-3.0 licenses, 
but the individual software may have additional license terms (see in particular
the Licenses in the [mib-container repository README](https://github.com/rosalindfranklininstitute/mib-container)).

### napari-clemreg-empanada

#### Version Information
Napari 0.4.18 with CLEM-Reg 0.2.1 and Empanada 1.2. 

#### Repository URLs

- [Docker file (GitHub)](https://github.com/rosalindfranklininstitute/napari-clemreg-empanada)
- [Container Image (Quay.io)](https://quay.io/repository/rosalindfranklininstitute/napari-clemreg-empanada)

#### Apptainer
```
apptainer run --nv \
  docker://quay.io/rosalindfranklininstitute/napari-clemreg-empanada:latest
```

#### Docker
```
docker run \
  --gpus all \
  -v /path/to/data:/data \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  --device=/dev/dri:/dev/dri \
  quay.io/rosalindfranklininstitute/napari-clemreg-empanada:latest
```

#### Test Usage
Refer to [Using Napari](https://rosalindfranklininstitute.github.io/volume-em-container-documentation/software/napari/#using-napari)
on the documentation with [Sample Data](https://zenodo.org/records/7936982) from
CLEM-Reg.

### fiji-mobie

#### Version Information
Fiji 2.16.1 (ImageJ2 2.16.0/1.54p) with MoBIE 4.0.3.

#### Repository URLs

- [Docker file (GitHub)](https://github.com/rosalindfranklininstitute/fiji-mobie)
- [Container Image (Quay.io)](https://quay.io/repository/rosalindfranklininstitute/fiji-mobie)

#### Apptainer
```
apptainer run docker://quay.io/rosalindfranklininstitute/fiji-mobie
```

#### Docker
```
docker run \
  -v /path/to/data:/data \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  --device=/dev/dri:/dev/dri \
  quay.io/rosalindfranklininstitute/fiji-mobie
```

#### Test Usage
Refer to [Loading image data](https://rosalindfranklininstitute.github.io/volume-em-container-documentation/software/fiji/#loading-image-data)
on the documentation using [Sample Data](https://bridges.monash.edu/articles/dataset/Fiji_Manual_Resources_v6_5_/21902043?file=38851446)
from Monash University.

### imod

#### Version Information
IMOD 5.1.2 with CUDA 12.

#### Repository URLs

- [Docker file (GitHub)](https://github.com/rosalindfranklininstitute/imod-container)
- [Container Image (Quay.io)](https://quay.io/repository/rosalindfranklininstitute/imod-container)

#### Apptainer
```
apptainer shell --nv docker://quay.io/rosalindfranklininstitute/imod-container
```

#### Docker
```
docker run \
  --gpus all \
  -v /path/to/data:/data \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  --device=/dev/dri:/dev/dri \
  quay.io/rosalindfranklininstitute/imod-container
```

#### Test Usage
Start the main executable by running `3dmod` in the interactive shell, as
described on [Starting with 3dmod](https://rosalindfranklininstitute.github.io/volume-em-container-documentation/software/imod/#starting-with-3dmod)
with [Sample Data](https://bio3d.colorado.edu/imod/files/imod_data.tar.gz) from
the University of Colorado. 

### mib2

#### Version Information
MIB 2.9102 (2025-05-08).

#### Repository URLs

- [Docker file (GitHub)](https://github.com/rosalindfranklininstitute/mib-container)
- [Container Image (Quay.io)](https://quay.io/repository/rosalindfranklininstitute/mib-container)

#### Apptainer
```
apptainer run \
    --nv \
    --env DISPLAY=$DISPLAY \
    --bind /tmp/.X11-unix:/tmp/.X11-unix \
    docker://quay.io/rosalindfranklininstitute/mib-container
```

#### Docker
```
docker run \
  --gpus all \
  -v /path/to/data:/data \
  -e DISPLAY=$DISPLAY \
  -v /tmp/.X11-unix:/tmp/.X11-unix \
  --device=/dev/dri:/dev/dri \
  quay.io/rosalindfranklininstitute/mib-container
```

#### Test Usage
Refer to [interacting with the
application](https://rosalindfranklininstitute.github.io/volume-em-container-documentation/software/mib2/#interacting-with-the-application)
on the MIB2 documentation page with [Sample
Data](https://zenodo.org/records/7936982) from CLEM-Reg.

## Feedback and License Information
For questions, suggestions, or feedback you can create an issue on the
[documentation repository](https://github.com/rosalindfranklininstitute/volume-em-container-documentation)
or the repository for one of the containers.
