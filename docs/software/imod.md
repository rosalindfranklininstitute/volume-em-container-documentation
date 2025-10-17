# IMOD
## Introduction
IMOD is a suite of modelling, display and image processing programs for 3D
reconstruction of electron microscopy and optical sections. It has a long
history, being developed primarily by researchers at the University of Colorado
since 1994.


## Using IMOD
**Prerequisites**:
IMOD provides a range of command-line and GUI-based programs.
It can be run without a GPU, but GPU acceleration is recommended for many
processing tasks such as tomogram reconstruction an alignment. 
GPU support requires an NVIDIA card with drivers supporting CUDA 12.0 or later
(most modern cards).


We cover how to run the containers and a practical example using the 
`3dmod` executable. Detailed instructions for the large number of tools
IMOD provides can be found in the [IMOD user manual](https://bio3d.colorado.edu/imod/doc/guide.html).


### Running the Container
Due to the large number of IMOD components to chose from, the recommended way to
use the RFI IMOD container is to start a shell (bash) session inside the
container, from which all IMOD programs are available.

In Apptainer, this is done using
```
apptainer shell --nv docker://quay.io/rosalindfranklininstitute/imod-container
```
If you don't have a NVIDIA GPU, omit the `--nv` option. 

A list of available IMOD programs can then be found by entering
```
ls $IMOD_DIR/bin
```
All of these commands are available from the command line within the container.
For example, `3dmod` will launch the 3dmod program and `etomo` will launch the
etomo program. 

For the equivalent in Docker, first enable containers to access the host
display by running `xhost +local:` (or `xhost +` for a remote host). Then run:
```
docker run -it  \
   --gpus all \
   -e DISPLAY=$DISPLAY \
   -v /tmp/.X11-unix:/tmp/.X11-unix \
   --device=/dev/dri:/dev/dri \
   quay.io/rosalindfranklininstitute/imod-container \
   bash
```
Where you can omit the `--gpus all` flag if you don't have a NVIDIA GPU.

To mount additional data directories e.g. a network drive, add
`--bind /path/to/files:/mnt` in Apptainer or 
`-v /path/to/files:/mnt`. Here `/path/to/files` is a location on your machine
and `/mnt` where the data will appear in the container.

### Starting with 3dmod

`3dmod` is IMOD's visualisation and modelling program and for many the primary
component of IMOD they will use. 3dmod primarily works with MRC files, which are
a standard format for storing 3D image data in electron microscopy, but can also
open individual TIFF images. 

Enter `3dmod` in the container terminal window to start the application:

![3dmod Startup](image/imod-3dmod-startup.png "3dmod Startup Window")

For a test example, you can download the sample dataset provided the University
of Colorado: [imod_data.tar.gz](https://bio3d.colorado.edu/imod/files/imod_data.tar.gz).
Unzip this and use `Select` in the 3dmod image to select one of the example
`.mrc` files. Pressing `OK` launches the main 3dmod program.

![3dmod Example](imod-3dmod-golgi.png "3dmod Example")

The information window on the left is the main point of control and information
when working with model data. The image display on the right can be panned 
and zoomed using the mouse with toolbar options to perform
annotations. A complete tutorial for using these functions can be found
on the University's [Introduction to 3dmod](http://bio3d.colorado.edu/imod/doc/3dmodguide.html).
Another helpful resource is Andrew Noske's [IMOD Wiki](https://andrewnoske.com/wiki/IMOD).

!!! tip
    The [`tif2mrc` tool](https://bio3d.colorado.edu/imod/doc/man/tif2mrc.html)
    can be used to convert a sequence of `.tiff` files into a `.mrc` file.

## Troubleshooting and Support
<!--### Known Issues-->
### Getting help
We are currently working with users to determine potential issues with the IMOD container
and steps to resolve them.

If you encounter  problems with the IMOD container,
[Create an issue](https://github.com/rosalindfranklininstitute/imod-container/issues/new)
on the container GitHub repository. Please share as much information 
as you can regarding your host system (operating system, container runtime),
the commands you are trying to run as well as any error messages.

## Version and license information
The container provides IMOD 5.1.2 with CUDA 12.0.
It is distributed under an [Apache 2.0 License](https://github.com/rosalindfranklininstitute/fiji-mobie?tab=Apache-2.0-1-ov-file).
Bibliographic information for IMOD can be found on the [IMOD Home Page](http://bio3d.colorado.edu/imod/).

## 🔗 Useful Links

- [IMOD Home Page](https://bio3d.colorado.edu/imod/)
- [IMOD User Guide](https://bio3d.colorado.edu/imod/doc/guide.html)
- [3dmod Guide](http://bio3d.colorado.edu/imod/doc/3dmodguide.html)
- [IMOD Tutorial by Andrew Noske](https://andrewnoske.com/wiki/IMOD_-_tutorial)
