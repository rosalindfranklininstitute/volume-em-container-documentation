# Introduction
## Overview
This documentation provides a guide for using containerised software environments
hosted by the [Rosalind Franklin Institute](https://rfi.ac.uk/) (RFI) for volume
electron microscopy (EM) analysis. 
This work is part of the [Collaborative Computational Project for EM](https://www.ccpem.ac.uk/)
project, which aims to support the community by unifying and improving access to
important volume EM software. 

## Quickstart
New to containerisation? We recommend reading our [*Using Containers*](containers.md)
page which includes an introduction to the popular container engines Apptainer
and Docker. 


## Requirements
Running the containers requires a working [Apptainer or Docker
installation](containers.md#running-containers). Many volume EM software
packages can leverage a GPU to accelerate processing, and some may not function
without one (look at for a warning ⚠️ note in the documentation).  GPU support is
mainly limited to NVIDIA cards on Windows or Linux systems, i.e., users with AMD
hardware or macOS may not be able to utilise GPU processing.

## Deployed software
Containers have been created for the following software:

- [Napari with Clemreg and Empanada Plugins](../software/napari.md)
- [Fiji with MoBie](../software/fiji.md)
- [IMOD](../software/imod.md)
- [MIB2](../software/mib2.md)

This documentation currently covers the Napari container, with
details of the others to be added in future updates.

!!! info
    This documentation is released under a Creative Commons Attribution-Share
    Alike 4.0 International License (CC BY-SA 4.0).  License information for
    individual software is linked on the relevant page. 

