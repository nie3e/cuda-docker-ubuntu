# CUDA with docker installation - Ubuntu Server 22.04
Cheat sheet for fast system installation with docker for AI.

# Prerequisities
Ubuntu Server 22.04 LTS.

Do not install docker with ubuntu.

# Steps after system installation

## Step 1 - install dev software
```
sudo apt-get update
sudo apt-get install gcc make docker.io
```

## Step 2 - disable Nouveau kernel driver
Recommended to install nvidia drivers.

https://askubuntu.com/a/868209

>Insert follow lines to the /etc/modprobe.d/blacklist.conf:
```
blacklist nouveau
blacklist lbm-nouveau
options nouveau modeset=0
alias nouveau off
alias lbm-nouveau off
```
>save and exit.
>
>Disable the Kernel nouveau by typing the following commands(nouveau-kms.conf may not exist,it is ok):
```
echo options nouveau modeset=0 | sudo tee -a /etc/modprobe.d/nouveau-kms.conf
```
>build the new kernel by:
```
sudo update-initramfs -u
sudo reboot
```

## Step 3 - install CUDA
https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=22.04&target_type=runfile_local

```
wget https://developer.download.nvidia.com/compute/cuda/12.1.0/local_installers/cuda_12.1.0_530.30.02_linux.run
sudo sh cuda_12.1.0_530.30.02_linux.run
```

Check installation:
```
nvcc --version
```
if it doesn't work:
```
sudo ln -s /usr/local/cuda-12.1/bin/nvcc /usr/bin/nvcc
```

## Step 4 - install NVIDIA container toolkit
```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/experimental/$distribution/libnvidia-container.list | \
         sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
         sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
		 
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
sudo nvidia-ctk runtime configure --runtime=docker
sudo systemctl restart docker
```

## Step 5 - verify installation
```
sudo docker run --rm --gpus all nvidia/cuda:12.1.0-runtime-ubuntu18.04 nvidia-smi
```
