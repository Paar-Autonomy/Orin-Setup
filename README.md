
# Setting up the NVIDIA Jetson Orin Nano

This tutorial was written for the NVIDIA Jetson Orin Nano Developer Kit (8 GB). The tutorial was written on `2024-2-13:19-42-5`. Details of the board are:

| Key | Value |
| --- | --- |
| P-Number | p3767-0005 |
| Module | NVIDIA Jetson Orin Nano (Developer kit) |
| SoC | tegra234 |
| CUDA Arch BIN | 8.7 |
| L4T | 36.2.0 |
| Jetpack | 6.0 DP |
| Machine | aarch64 |
| System | Linux |
| Distribution | Ubuntu 22.04 Jammy Jellyfish |
| Release | 5.15.122-tegra |
| Python | 3.11.5 |
| CUDA | 12.2.140 |
| OpenCV | 4.8.0 |
| OpenCV-Cuda | False |
| cuDNN | 8.9.4.25 |
| TensorRT | 8.6.2.3 |
| VPI | 3.0.10 |
| Vulkan | 1.3.204 |

__IMPORTANT WARNING__: The host must be running on __Ubuntu 20.04__ to flash the board. 


### Flashing the Board


The board can be flashed using the SDK Manager. The SDK Manager can be downloaded from the [NVIDIA website](https://developer.nvidia.com/nvidia-sdk-manager). To flash the board, force the board into recovery mode by following the steps in [JetsonHacks Tutorial](https://www.youtube.com/watch?v=q4fGac-nrTI&t=218s). Then, select the following options:

- Host Machine: Ubuntu 20.04
- Target Hardware: Jetson Orin Nano Developer Kit 8 GB
- Target OS: Jetpack 6.0 DP
- DeepStream: 6.0

In my case, I had already mounted an `NVME SSD` as a storage component. Most tutorials do SD cards, but I went with a mainstream 1 TB NVME SSD for performance reasons. Select the `pre-config` option to set the board and initialize it with a username and a password. For simplicity, the username and password are set to `nvidia`. The board will be flashed, and it will be ready to use.


### Setting up the Board

First, update the system:
```bash
sudo apt-get update
sudo apt-get upgrade
sudo reboot
```

Then, install the following packages:
```bash
sudo apt-get upgrade
sudo apt install ssh firefox zlib1g 
sudo apt-get install python3 python3-dev python3-distutils python3-venv python3-pip
sudo apt-get install ninja-build
sudo apt install software-properties-common lsb-release
sudo apt install cmake
sudo apt install build-essential libtool autoconf unzip wget
sudo apt-get install terminator
```

These packages will prepare the board for development. If you are working extensively with Python, install `conda` package manager. The following commands will install `miniconda`:
```bash
cd Downloads/
sudo apt upgrade
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-aarch64.sh
chmod +x Miniconda3-latest-Linux-aarch64.sh
./Miniconda3-latest-Linux-aarch64.sh
```


### Checking CUDA and cuDNN

Before we advance on checking CUDA and cuDNN, we need to verify `gcc` and `nvidia-smi`:
```bash
gcc --version
nvidia-smi
```

We will begin with checking CUDA:
```bash
git clone https://github.com/NVIDIA/cuda-samples.git
cd cuda-samples/Samples/1_Utilities/deviceQuery/
make
./deviceQuery
```

After checking CUDA, we will check cuDNN:
```bash
cp -r /usr/src/cudnn_samples_v8/ ~/Documents/workspace/
cd cudnn_samples_v8/mnistCUDNN/
sudo apt install libfreeimage3 libfreeimage-dev
sudo apt-get update
sudo apt-get upgrade
make clean && make
./mnistCUDNN
```


### Monitoring the Board

To monitor the board, we will install `jetson-stats`:
```bash
sudo pip3 install -U jetson-stats
```

To check your board details and version of different software using `jtop`, as well as the usage across its computing resources and power consumption, there are some Python scripts that use `jtop`. For example [`jtop_properties.py`](https://github.com/rbonghi/jetson_stats/blob/master/examples/jtop_properties.py) is a quick way to monitor the aforementioned.


### VS Code

If you are a Visual Studio Code user, it is supported on the Jetson. Run the following commands:

```bash
cd Downloads/
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg
sudo install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/
sudo sh -c 'echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/trusted.gpg.d/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" > /etc/apt/sources.list.d/vscode.list'
rm -f packages.microsoft.gpg
sudo apt install apt-transport-https
sudo apt update
sudo apt install code
```


### Board Case

For my board, I bought the [Yahboom CUBE nano case](https://www.amazon.com/Yahboom-Dissipation-Protect-Cooling-Antenna/dp/B0CD71X8SV). On [their page](http://www.yahboom.net/study/CUBE_NANO), there are also [tutorials](https://youtu.be/anbMcWsagn8) and [code](https://drive.google.com/drive/folders/1A4L1ec-Na1_K0K1LXdnzSCva2iZ02YVX) for setting up the case and configuring the OLED screen that comes with it. Finally, there is also a [GitHub repo](https://github.com/YahboomTechnology/Jetson-CUBE-case) associated with the case.


### Notes

It is important to note that the NVIDIA SDK Manager must be installed on an Ubuntu 20.04 engine. I tried two different machines running Ubuntu 22.04 and attempted to flash the board, but it would yield errors. I also tried Ubuntu 18.04, but the latest supported Jetpack was `5.x.y`, and at the moment of writing, the latest Jetpack is `6.z`. Therefore, the host machine must be running Ubuntu 20.04.
