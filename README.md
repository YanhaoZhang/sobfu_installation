# sobfu_installation
This is the dependencies I installed for running code [sobfu](<https://github.com/dgrzech/sobfu>). Briefly, the dependencies are: **CUDA 9.0**, **PCL-1.8.1**,  **Eigen-3.3.6**, **Boosct-1.65.1**, **OpenCV-3.4.3**, and **VTK-7.1.1**. 

This will also be helpful for someone who want to install NVIDIA driver and CUDA on a laptop with multiple GPU.

## Dependencies
### 1. update gcc
cuda 9.0 is only supported by gcc 6. The gcc should be changed firstly.
```shell
sudo apt-get update && \
sudo apt-get install build-essential software-properties-common -y && \
sudo add-apt-repository ppa:ubuntu-toolchain-r/test -y && \
sudo apt-get update && \
sudo apt-get install gcc-6 g++-6 -y && \
sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-6 60 --slave /usr/bin/g++ g++ /usr/bin/g++-6 && \
gcc -v
```

### 2. install nvidia driver and cuda 9.0
Do not change anything, just follow this instruction [cuda_installation_on_ubuntu_18.04](<https://gist.github.com/Mahedi-61/2a2f1579d4271717d421065168ce6a73>). It also works for Ubuntu 16.04.
```shell
# first get the PPA repository driver
sudo add-apt-repository ppa:graphics-drivers/ppa

# install nvidia driver 
sudo apt install nvidia-384 nvidia-384-dev

# install other import packages
sudo apt-get install g++ freeglut3-dev build-essential libx11-dev libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev

# downoad one of the "runfile (local)" installation packages from cuda toolkit archive 
wget https://developer.nvidia.com/compute/cuda/9.0/Prod/local_installers/cuda_9.0.176_384.81_linux-run

# make the download file executable
chmod +x cuda_9.0.176_384.81_linux.run 
sudo ./cuda_9.0.176_384.81_linux.run --override

# answer following questions while installation begin
# You are attempting to install on an unsupported configuration. Do you wish to continue? y
# Install NVIDIA Accelerated Graphics Driver for Linux-x86_64 384.81? n
# Install the CUDA 9.0 Toolkit? y

# set up symlinks for gcc/g++
sudo ln -s /usr/bin/gcc-6 /usr/local/cuda/bin/gcc
sudo ln -s /usr/bin/g++-6 /usr/local/cuda/bin/g++

# setup your paths
echo 'export PATH=/usr/local/cuda-9.0/bin:$PATH' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-9.0/lib64:$LD_LIBRARY_PATH' >> ~/.bashrc
source ~/.bashrc

# Finally, to verify the installation, check
nvidia-smi
nvcc -V
```


### 3. install cmake
Here I chose the latest version (3.15.0). Download [here](<https://cmake.org/download/>) and follow the [cmake github instruction](<https://github.com/Kitware/CMake>). 
```shell
./bootstrap && make && sudo make install
```

### 4. install opengl
```shell
sudo apt-get install libgl1-mesa-dev
sudo apt-get install libglu1-mesa-dev
sudo apt-get install freeglut3-dev
```

### 5. install boost

Download [boost 1.65.1](<https://www.boost.org/users/history/version_1_65_1.html>).

```shell
sudo ./bootstrap.sh
sudo ./b2 install
sudo ldconfig
# setup boost
echo 'export CPLUS_INCLUDE_PATH=/usr/local/lib/boost/include' >> ~/.bashrc
```

### 6. install Eigen 3.3.6
```shell
mkdir build
cd build
cmake ..
make -j4
sudo make install
```

### 7. install vtk 7.1.1
```shell
mkdir build
cd build
cmake ..
make -j4
sudo make install
```

### 8. install pcl 1.8.1
download pcl from [here](<https://github.com/PointCloudLibrary/pcl/releases/tag/pcl-1.8.1>). 
```shell
sudo apt-get update  
sudo apt-get install git build-essential linux-libc-dev
# sudo apt-get install cmake cmake-gui
sudo apt-get install libusb-1.0-0-dev libusb-dev libudev-dev

# openmpi-common error: install again is fine
sudo apt-get install mpi-default-dev openmpi-bin openmpi-common 
sudo apt-get install libflann1.8 libflann-dev
# sudo apt-get install libeigen3-dev 
# sudo apt-get install libboost-all-dev
# sudo apt-get install libvtk5.10-qt4 libvtk5.10 libvtk5-dev
sudo apt-get install libqhull* libgtest-dev
sudo apt-get install freeglut3-dev pkg-config
sudo apt-get install libxmu-dev libxi-dev
sudo apt-get install mono-complete
# sudo apt-get install qt-sdk 
sudo apt-get install openjdk-8-jdk openjdk-8-jre
sudo apt-get install libflann-dev

mkdir build 
cd build
cmake -DCMAKE_BUILD_TYPE=Release \ -DBUILD_GPU=ON -DBUILD_apps=ON ..
make -j4
sudo make install
```

### 9. install OpenCV 3.4.3
According to [opencv tutorial_linux_install](<https://docs.opencv.org/3.4.3/d7/d9f/tutorial_linux_install.html>). 
```shell
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D BUILD_PYTHON_SUPPORT=ON -D BUILD_EXAMPLES=ON –D WITH_VTK=ON ../
make -j4
sudo make install
```

### 10. Some other things
```shell
sudo apt-get update
sudo apt-get install libopenni2-dev
sudo apt-get install libopenni-dev
sudo apt-get install libpcap-dev
sudo apt-get install libpng-dev
```

## Run sobfu
Download [sobfu](<https://github.com/dgrzech/sobfu>) and some useful [instructions](<https://github.com/dgrzech/sobfu/issues/3>)

Modify [this line](<https://github.com/dgrzech/sobfu/blob/master/CMakeLists.txt#L41>) in the CmakeList according to the GPU in your laptop. The GPU I got is GeForce GTX 960M, so this line should be `-gencode;arch=compute_50,code=sm_50`. Some settings for GPU can be found on the wiki of [ccminer](<https://github.com/tpruvot/ccminer/wiki/Compatibility>). 

Run the code
```shell
./build/bin/app /path/to/data /path/to/params <--enable-viz> <--enable-log> <--verbose>
```
