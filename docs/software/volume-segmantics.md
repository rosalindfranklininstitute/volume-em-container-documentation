# Volume Segmantics
## Introduction
Volume Segmantics is a toolkit for semantic segmentation of volumetric
data using PyTorch deep learning models. It provides a simple
command-line interface and API that allows researchers to quickly
train a variety of 2D PyTorch segmentation models on 3D datasets,
and then use those trained models to segment larger datasets.

Volume Segmantics was originally developed by Oliver King, Dimitrios
Bellos and Mark Basham at the Rosalind Franklin Institute in 2022[^1],
and is now developed and maintained by Avery Pennington.

## Using Volume Segmantics
**Prerequisites**:
The Volume Segmantics container uses CUDA-12.x enabled PyTorch, which requires a
Nvidia GPU with a reasonably modern (525+) driver. Training on large
datasets may require significant VRAM (e.g. 40G+).

### Running the Container
The Volume Segmantics contain can be run either using Apptainer:
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
Note here the `ipc=host` flag allows the container access to the host
shared memory, since the default provision (64MB) in Docker is typically
insufficient for training. It is also necessary to
create a volume mount to access your data and model configuration
files (apptainer provides access
to your home directory by default, but additional locations can be
added with `--bind /path/to/data:/data`).

!!! info
    If you do not wish to give the container shared access to the host 
    shared memory in Docker, you can portion a specific amount for the
    contain using `--shm-size=`, e.g., `--shm-size=8g`.

### Using Volume Segmantics

Starting the Volume Segmantics container gives a Bash Shell with
the two main commands, `model-train-2d` and `model-predict-2d`,
for training a 2d model on a 3d image and for using a 2d model 
for 3d volume segmentation prediction, respectively:
```
model-train-2d --data path/to/image/data.tiff --labels path/to/corresponding/segmentation/labels.tiff
model-predict-2d path/to/model_file.pytorch path/to/data_for_prediction.tiff
``` 
For detailed instructions on using these commands, refer to the
[Volume Segmantics
README](https://github.com/rosalindfranklininstitute/volume-segmantics),
and the associated
[tutorial](https://github.com/rosalindfranklininstitute/volume-segmantics/blob/main/training_data/README.md) with example data.
 Here we note that
these commands require access to settings files
in a directory `./volseg-settings` available from where
the commands are run:
```
./volseg-settings/2d_model_train_settings.yaml
./volseg-settings/2d_model_predict_settings.yaml
```
You can view the default settings values on the 
project's GitHub repository
[volseg-settings](https://github.com/rosalindfranklininstitute/volume-segmantics/blob/main/training_data/README.md).
They may also be found under `/opt/` of the container,
so can be conveniently copied to your current working 
directory:
```
$ cp -r /opt/volume-segmantics/volseg-settings ./
```

## Troubleshooting and Support
### Getting help
If you encounter problems using Volume Segmantics, you can
[create an issue](https://github.com/rosalindfranklininstitute/volume-segmantics/issues/new)
on the tool's GitHub repository. If the issue relates to a potential
software bug, crash etc., please share as much information as you can
regarding your host system (e.g., Nvidia card and driver,
operating system and container
runtime), the commands you are trying to run as well as any error
messages.

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

## 🔗 Useful Links

- [RFI PhD Training
  Course](https://github.com/rosalindfranklininstitute/rfi-phd-training/blob/main/Segmentation_with_Volume_Segmantics/Segmentation_with_Volume_Segmantics.md) on Volume Segmantics
- [Volume Segmantics Tutorial](https://github.com/rosalindfranklininstitute/volume-segmantics/blob/main/training_data/README.md)
