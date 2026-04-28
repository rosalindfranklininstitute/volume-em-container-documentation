# Volume Segmantics
*Please note this page is currently under construction*
## Introduction
Volume Segmantics is A toolkit for semantic segmentation of volumetric
data using PyTorch deep learning models. It provides a simple
command-line interface and API that allows researchers to quickly
train a variety of 2D PyTorch segmentation models on 3D datasets,
and then use those trained models to segment larger datasets.

Volume Segmantics was originally developed by Oliver King, Dimitrios
Bellos and Mark Basham at the Rosalind Franklin Institute (see
citations) and is since developed and maintained by Avery Pennington.

## Using Volume Segmantics
**Prerequisites**:
The Volume Segmantics container uses CUDA-12.x enabled PyTorch, which requires a
Nvidia GPU with a reasonably modern (525+) driver. Training on large
datasets may require significant VRAM (e.g. 40G+).

### Running the Container
The Volume Segmantics contain can be run either using Apptainer,
```
apptainer run --nv docker://quay.io/rosalindfranklininstitute/volume-segmantics
```
Or using docker:
```
docker run \
  --gpus all \
  --ipc=host \
  -v /path/to/data:/data
  quay.io/rosalindfranklininstitute/volume-segmantics
```
Note for Docker the `ipc=host` flag allows the container access to the host
shared memory, since the default provision (64MB) is not
sufficient for running Volume Segmantics. It is also necessary to
create a volume mount to access your data (apptainer provides access
to your home directory by default, but additional locations can be
added with `--bind /path/to/data:/data`).

!!! info
   If you do not wish to give the container access to the entire host 
   shared memory, you can allocate a specific amount using the
   `--shm-size=` option.

### Using Volume Segmantics
## Troubleshooting and Support
### Getting help
If you encounter problems using Volume Segmantics, yu can
[create an issue](https://github.com/rosalindfranklininstitute/volume-segmantics/issues/new)
on the projects GitHub repository. If the issue is technical (e.g.
installation, crash), please share as much information 
as you can regarding your host system (e.g., operating system, container runtime),
the commands you are trying to run as well as any error messages.

## Version and license information
The [RFI container](https://github.com/rosalindfranklininstitute/volume-segmantics-container)
 provides 0.4.0b released April 2026.

Volume Segmantics is distributed under an [Apache
2.0 License](https://github.com/rosalindfranklininstitute/volume-segmantics/blob/main/LICENSE.md).
If using VolumeSegmnatics in your work, consider citing 
the Volume Segmnatics publication[^1] as well as those of the
Albumentations[^2] and MONAI[^3] libraries the tool utilises.

[^1]: King O.N.F, Bellos, D. and Basham, M., [Software 7(78), 4691 (2022)](https://doi.org/10.21105/joss.04691)
[^2]: A. Buslaev et al., [Information 11(2), 125 (2020)](https://doi.org/10.3390/info11020125)
[^3]: M. Jorge Cardoso et al., [arXiv:2211.02701 (2022)](https://arxiv.org/abs/2211.02701)
I. Belevich and E. Jokitalo, [PLoS Comput Biol., 17(3):e1008374 (2021)](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1008374)
