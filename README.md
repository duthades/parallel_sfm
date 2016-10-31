# parallel_sfm

## OpenCV Installation

```
 sudo apt-get install build-essential
 sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
 sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev```
 
cd ~/<my_working _directory>
git clone https://github.com/opencv/opencv.git

cmake [<some optional parameters>] <path to the OpenCV source directory>

cd ~/opencv
mkdir release
cd release
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local .. <path to opencv source code>

make
sudo make install

## Dependenci
