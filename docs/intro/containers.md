# Using Containers
## Why Containers? Simple Sandboxed Software
A container image is a bundle of software that includes all the code and
dependencies needed to run an application.  An image can be used by a program
called a container runtime to run the application in an isolated environment,
providing an efficient means to consistent execution across many different host
hardware and software configurations.

The primary advantages of using containers to package scientific software are: 

1. **Reproducibility**: Application code and dependencies are
version-controlled such that the exact software environment used to create
or process a dataset can be repeated 
2. **Portability**: Containers can be deployed on a wide range of systems,
from local workstations to HPC clusters and cloud platforms. This is
especially useful when researchers have limited ability to perform
software installations on remote computing environments 

Further benefits include:

- **Ease of use**: Containers can be deployed quickly and run with
 minimal expertise, avoiding complex installation procedures and
  "works on my machine" problems 
- **Scalability**: Many container instances can be spawned simultaneously
 to scale up computation (e.g., using [Kubernetes](https://rosalindfranklininstitute.github.io/rsecon25-intro-to-kubernetes/why-kubernetes/))
- **Dependency isolation and security**: Container dependencies, configuration
   and running processes are effectively isolated from the host system and other 
   active containers
<!-- Efficiency: image layering and caching etc.-->
### Limitations

- While containers aim for portability across operating systems and hardware,
in practice there are cases where containers may fail to run on certain
systems, or with different container runtimes. GPU support can be particularly
problematic 
<!-- Most containers require some interaction with the host system and files, so are not completely isolated.-->
- Due to the ephemeral nature of containers, user settings such as
theme, keyboard layout and language will normally be lost each time the container
is shutdown and restarted.

Finally, while providing a simple interface to use, by abstracting 
the application installation and configuration containers can
make troubleshooting difficult, especially for those unfamiliar with
containerised software. 

## How containers are made
Container images are built from a set of human-readable instructions known as a 
**build file**. These instructions include:

1. Selecting a base image (e.g., a minimal operating system)
2. Installing dependencies and required libraries
3. Adding the target application software
4. Configuring the runtime environment

Container software such as Docker or Apptainer reads the
instructions to build a container **image**. Images may be assigned version tags
(e.g. `v1.0` or `latest`) and stored locally or uploaded to online container
registries to make the software more widely available. The containers described
in this documentation are hosted on the Rosalind Franklin Institute's [quay.io
repository](https://opencontainers.org/), for example.

!!! info
    The [Open Container Initiative](https://opencontainers.org/) defines
    standards for building, running and distributing containers. These
    standards are supported by most container runtime solutions. 

## Running containers 

In order to run a container image you need container runtime. Our containers
have been primarily tested against Apptainer, which is common in research
computing and HPC environments, but we will also provide commands for Docker,
the most popular containerisation tool across software development and industry.

### Apptainer
Apptainer (formerly Singularity) is designed for multi-user HPC environments.
It produces portable `.sif` file that can be
easily shared and executed without requiring root privileges.

#### Installation
It you are in a research environment, it is quite likely Apptainer is already
available for you to use&mdash;ask you local research software engineer or
system administrator. For advanced users, the official Apptainer documentation
has [installation
instructions](https://apptainer.org/docs/admin/main/installation.html) for
Linux, Windows and MacOS.

!!! tip
    Apptainer is provided for RFI users via a module share system, where
    Apptainer is loaded by running `module load apptainer` (RFI users may
    view the [AI Docs](https://aidocs.zaphod.rfi.ac.uk/Software/module-share/) for further
    information). Other services may make Apptainer available by default.

#### Usage
The two most common commands are `pull`, to download a container image from a
remote repository, and `run`, to start a container using an image:
```
apptainer pull image.sif docker://quay.io/repository/image-name:tag 
apptainer run image.sif
```
In fact, an image URL can be provided directly to the `run` command,
skipping the need for a separate `pull` (if the image has not previously
been downloaded, a pull occurs automatically). This also avoids the need
to manage the location of the downloaded image file. The `tag` is optional
and normally defaults to `latest`, i.e., the most recent release marked as stable
by the developer.

The `run` command may also accept additional arguments passed to
the application on startup (depending on the specific software)
as well as options. An important option is
 `--nv` (as in `apptainer run --nv...`),
which allows the container to access GPUs on the host.
 
For troubleshooting, `apptainer shell image.sif`
 is useful as it launches 
an interactive shell which can be used to inspect the container filesystem,
e.g., for application logs.
<!--(or `exec image-name /bin/bash`) --> 

<!-- cleanup, shutdown? -->

### Docker
Docker requires administrator or root privileges to run, which limits its use in
shared HPC environments. However, it remains a popular tool for local
development and workstation use.

#### Installation
Docker Desktop is a convenient way for new users to install
Docker&mdash;see  the [Docker docs](https://docs.docker.com/get-started/get-docker/).
for installation instructions on all operating systems.

#### Usage
Docker has `pull` and `run` commands analogous to Apptainer:
```
docker pull docker://quay.io/repository/image-name:tag 
docker run image-name:tag
```
You do not need to specify an image output file; the image
is stored and managed automatically by Docker.

An interactive shell may be 
started with a container using
`docker run -it image-name:tag /bin/bash`, or running
`docker exec -it container-name /bin/bash` with a container
that is already running.

Docker on Windows/Linux [supports NVIDIA
GPUs](https://docs.docker.com/desktop/features/gpu/) with the `--gpus all`
option to `run` (`device=X` instead of `all` to select a specific GPU
number). This does require the [NVIDIA Container
Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/index.html)
to be installed. 

!!! tip
    Use the `--rm` option to `docker run` to have Docker automatically remove
    the container filesystem and resources after the contain shutdown.
    Alternatively run `docker container prune` periodically to clean-up inactive
    containers.

## Bind mounts 
To work with your data, containers need access to files on your host system.
Apptainer provides access to common locations including `home/$USER`, `/tmp`
and the current working directory. To provide access to additional directories,
they must be mounted when starting the container using `--bind`:
```
apptainer run --bind /path/on/host:/path/in/container image.sif/image-url
```
For example,
```
apptainer run --bind /ceph/users/username:/mnt image.sif/image-url
```
mounts a CephFS directory on the host to `/mnt` inside the container. 
In other words, `/mnt` is the directory to navigate to when
browsing files within the containerised application in order
to access files from  `/ceph/users/username`.

Docker requires explicit bind mounts for all host directories. Use the 
`--volume`/`-v` option:
```
docker run -v /path/on/host:/path/in/container image-name
``` 

## 📚 Further reading
- [Apptainer User Guide](https://apptainer.org/docs/user/main/index.html)
- [dockerdocs (official Docker documentation)](https://docs.docker.com/)
- Alternative container software: [podman](https://podman.io/) and
  [Shifter](https://docs.nersc.gov/development/containers/shifter/)
