# Ubuntu 22.04 for Deep Learning

In the name of God

This gist contains steps to setup `Ubuntu 22.04` for deep learning.

----------------------------------------------------------------------------------------------------

## Install Ubuntu 22.04

- Computer name: Name-PC
- Name: Name
- User name: name
- Password: ********

----------------------------------------------------------------------------------------------------

## Update Ubuntu

```sh
$ sudo apt update
$ sudo apt full-upgrade --yes
$ sudo apt autoremove --yes
$ sudo apt autoclean --yes
$ reboot
```

----------------------------------------------------------------------------------------------------

## Create Update Script

- Create a file (`~/full-update.sh`) with the following lines:

```sh
#!/bin/bash

if [ "$EUID" -ne 0 ]
  then echo "Error: Please run as root."
  exit
fi

clear

echo "################################################################################"
echo "Updating list of available packages..."
echo "--------------------------------------------------------------------------------"
apt update
echo "################################################################################"
echo

echo "################################################################################"
echo "Upgrading the system by removing/installing/upgrading packages..."
echo "--------------------------------------------------------------------------------"
apt full-upgrade --yes
echo "################################################################################"
echo

echo "################################################################################"
echo "Removing automatically all unused packages..."
echo "--------------------------------------------------------------------------------"
apt autoremove --yes
echo "################################################################################"
echo

echo "################################################################################"
echo "Clearing out the local repository of retrieved package files..."
echo "--------------------------------------------------------------------------------"
apt autoclean --yes
echo "################################################################################"
echo
```

----------------------------------------------------------------------------------------------------

## Change Settings

- Review Ubuntu Settings

----------------------------------------------------------------------------------------------------

## Change Software & Updates

- Review Software & Updates

----------------------------------------------------------------------------------------------------

## Update Ubuntu

- `$ sudo ~/full-update.sh`

----------------------------------------------------------------------------------------------------

## Install Chrome (https://www.google.com/chrome)

 - `$ sudo dpkg -i google-chrome-stable_current_amd64.deb`

----------------------------------------------------------------------------------------------------

## Install Development Tools

 - `$ sudo apt install build-essential pkg-config cmake cmake-qt-gui ninja-build valgrind`

----------------------------------------------------------------------------------------------------

## Install Python 3

- `$ sudo apt install python3 python3-wheel python3-pip python3-venv python3-dev python3-setuptools`

----------------------------------------------------------------------------------------------------

## Install Git

```sh
$ sudo apt install git
$ git config --global user.name "Name"
$ git config --global user.email "name@domain.com"
$ git config --global core.editor "gedit -s"
```
- Copy your own SSH keys to `~/.ssh/`

----------------------------------------------------------------------------------------------------

## Install NVIDIA Drivers for Deep Learning

**Check Display Hardware**:

- `$ sudo lshw -C display`

**Install NVIDIA GPU Driver**:

- Software & Updates > Additional Drivers > NVIDIA

Try `$ sudo ubuntu-drivers autoinstall` if NVIDIA drivers are disabled.

**Check TensorFlow and CUDA Compatibilities**:

- https://www.tensorflow.org/install/gpu
- https://www.tensorflow.org/install/source#gpu

**Install CUDA Toolkit (CUDA 11.2)**:

1. Install prerequisites:
	- `$ sudo apt install linux-headers-$(uname -r)`
2. Download CUDA 11.2 (https://developer.nvidia.com/cuda-toolkit-archive)
2. Install CUDA 11.2: `$ ./cuda_11.2.2_460.32.03_linux.run --override` (without Driver)
3. Set up the development environment by modifying the `PATH` and `LD_LIBRARY_PATH` variables (Add following lines to `~/.bashrc`):
	- `export PATH=$PATH:/usr/local/cuda-11.2/bin`
	- `export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.2/lib64:/usr/local/cuda-11.2/extras/CUPTI/lib64`
4. Check that GPUs are visible using the following command:
	- `$ nvidia-smi`

**Install cuDNN v8.1, for CUDA 11.2**:

- https://developer.nvidia.com/cudnn
- https://developer.nvidia.com/rdp/cudnn-archive
- `$ sudo dpkg -i libcudnn8_8.1.1.33-1+cuda11.2_amd64.deb`
- `$ sudo dpkg -i libcudnn8-dev_8.1.1.33-1+cuda11.2_amd64.deb`
- `$ sudo dpkg -i libcudnn8-samples_8.1.1.33-1+cuda11.2_amd64.deb # Optional`

**Reboot**:

- `$ reboot`

----------------------------------------------------------------------------------------------------

## Machine Learning Environment

```sh
$ python3 -m venv ~/venv/ml
$ source ~/venv/ml/bin/activate
(ml) $ pip install --upgrade pip setuptools wheel
(ml) $ pip install --upgrade numpy scipy matplotlib ipython jupyter pandas sympy nose
(ml) $ pip install --upgrade scikit-learn scikit-image
(ml) $ deactivate
```

----------------------------------------------------------------------------------------------------

## Deep Learning Environment (TensorFlow-CPU)

- https://www.tensorflow.org/install

```sh
$ python3 -m venv ~/venv/tfcpu
$ source ~/venv/tfcpu/bin/activate
(tfcpu) $ pip install --upgrade pip setuptools wheel
(tfcpu) $ pip install --upgrade opencv-python opencv-contrib-python
(tfcpu) $ pip install --upgrade tensorflow-cpu tensorboard keras
(tfcpu) $ deactivate
```

----------------------------------------------------------------------------------------------------

## Deep Learning Environment (TensorFlow-GPU)

- https://www.tensorflow.org/install/gpu

```sh
$ python3 -m venv ~/venv/tfgpu
$ source ~/venv/tfgpu/bin/activate
(tfgpu) $ pip install --upgrade pip setuptools wheel
(tfgpu) $ pip install --upgrade opencv-python opencv-contrib-python
(tfgpu) $ pip install --upgrade tensorflow-gpu tensorboard keras
(tfgpu) $ deactivate
```

Verification:

```sh
$ source ~/venv/tfgpu/bin/activate
(tfgpu) $ python
>>> from tensorflow.python.client import device_lib
>>> device_lib.list_local_devices()
>>> exit()
(tfgpu) $ deactivate
```

----------------------------------------------------------------------------------------------------

## Deep Learning Environment (PyTorch-CPU)

- https://pytorch.org/get-started/locally/

```sh
$ python3 -m venv ~/venv/torchcpu
$ source ~/venv/torchcpu/bin/activate
(torchcpu) $ pip install --upgrade pip setuptools wheel
(torchcpu) $ pip install --upgrade opencv-python opencv-contrib-python
(torchcpu) $ pip install --upgrade torch torchvision torchaudio --extra-index-url https://download.pytorch.org/whl/cpu
(torchcpu) $ deactivate
```

----------------------------------------------------------------------------------------------------

## Deep Learning Environment (PyTorch-GPU)

- https://pytorch.org/get-started/locally/

```sh
$ python3 -m venv ~/venv/torchgpu
$ source ~/venv/torchgpu/bin/activate
(torchgpu) $ pip install --upgrade pip setuptools wheel
(torchgpu) $ pip install --upgrade opencv-python opencv-contrib-python
(torchgpu) $ pip install --upgrade torch torchvision torchaudio
(torchgpu) $ deactivate
```

Verification:

```sh
$ source ~/venv/torchgpu/bin/activate
(torchgpu) $ python
>>> import torch
>>> torch.cuda.is_available()
>>> exit()
(torchgpu) $ deactivate
```

----------------------------------------------------------------------------------------------------

## Install Miniconda

- https://conda.io/
- https://docs.conda.io/en/latest/miniconda.html
- https://docs.conda.io/projects/conda/en/latest/user-guide/getting-started.html
- https://anaconda.org/search

Install:

```sh
$ wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
$ chmod +x Miniconda3-latest-Linux-x86_64.sh

$ ./Miniconda3-latest-Linux-x86_64.sh

$ # Do you wish the installer to initialize Miniconda3
  # by running conda init?
  # yes

$ source ~/miniconda3/bin/activate 
$ conda config --set auto_activate_base false
$ conda deactivate
```

Activate and Deactivate:

```sh
$ conda activate
(base) $ conda deactivate
```

Managing conda:

```sh
(base) $ conda info
(base) $ conda update conda
```

Managing environments:

```sh
(base) $ conda info --envs

(base) $ conda create --name snakes python=3.5
(base) $ conda info --envs

(base) $ conda activate snakes

(snakes) $ python --version

(snakes) $ conda search beautifulsoup4

(snakes) $ conda install beautifulsoup4
(snakes) $ conda list

(snakes) $ conda update beautifulsoup4

(snakes) $ conda uninstall beautifulsoup4
(snakes) $ conda list

(snakes) $ conda deactivate

(base) $ conda remove --name snakes --all

(base) $ conda info --envs
```

----------------------------------------------------------------------------------------------------

## Additional Useful Python Packages

* Project documentation with Markdown:
* https://www.mkdocs.org/
* https://squidfunk.github.io/mkdocs-material/

```sh
$ pip install mkdocs mkdocs-material
```

----------------------------------------------------------------------------------------------------

## Install Qt

- https://www.qt.io/
- https://doc.qt.io/qt-6/linux.html
- `$ sudo apt install build-essential libgl1-mesa-dev`

----------------------------------------------------------------------------------------------------

## Install PyCharm

- https://www.jetbrains.com/pycharm/

**Enable GPU support for PyCharm Projects**:

- Edit Configurations...
- Environment variables:
	- `PATH=$PATH:/usr/local/cuda-11.2/bin`
	- `LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/cuda-11.2/lib64:/usr/local/cuda-11.2/extras/CUPTI/lib64`

----------------------------------------------------------------------------------------------------

## Install Docker Engine & Docker Compose

- https://docs.docker.com/engine/install/ubuntu/
- https://docs.docker.com/compose/install/

Nvidia Container Toolkit:

- https://github.com/NVIDIA/libnvidia-container
- https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/overview.html

- https://github.com/NVIDIA/nvidia-docker/
- https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker
- https://hub.docker.com/r/nvidia/cuda

Make sure you have installed the NVIDIA driver and Docker engine for your Linux distribution Note that you do not need to install the CUDA Toolkit on the host system, but the NVIDIA driver needs to be installed

```sh
$ distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
    && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
    && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
        sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
        sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
   
$ sudo apt update

$ sudo apt install --yes nvidia-docker2

$ sudo systemctl restart docker

# NOTE: --runtime=nvidia
$ docker container run --rm --runtime=nvidia nvidia/cuda:11.0.3-base nvidia-smi
# OR
$ docker run --rm --gpus all nvidia/cuda:11.0.3-base nvidia-smi
```

TensorFlow Docker:

- https://www.tensorflow.org/install/docker
- https://hub.docker.com/u/tensorflow/tensorflow

```sh
# CPU
$ docker run -it tensorflow/tensorflow bash

# GPU
# NOTE: --runtime=nvidia
$ docker run --runtime=nvidia -it tensorflow/tensorflow:latest-gpu bash
```

Running ARM Docker Containers:

- https://en.wikipedia.org/wiki/QEMU
- https://en.wikipedia.org/wiki/Binfmt_misc
- https://wiki.debian.org/QemuUserEmulation
- https://github.com/multiarch/qemu-user-static
- https://github.com/docker-library/official-images#architectures-other-than-amd64

```sh
$ sudo apt install qemu qemu-user-static binfmt-support
$ # Host Architecture: x86_64
$ uname -m
$ # ARM Container Architecture: armv7l
$ docker run --rm arm32v7/debian uname -m
```

----------------------------------------------------------------------------------------------------

## Install Additional Tools

```sh
$ sudo apt install ubuntu-restricted-extras
$ sudo apt install virtualbox virtualbox-dkms virtualbox-ext-pack virtualbox-guest-additions-iso
$ sudo apt install curl wget uget tar zip unzip rar unrar
$ sudo apt install gimp vlc ffmpeg
$ sudo apt install kdiff3
```

- Ubuntu Software > System Load Indicator

----------------------------------------------------------------------------------------------------

## Install Additional Fonts

- `$ mkdir ~/.fonts`
- Copy fonts to `~/.fonts`
