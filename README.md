# parallel_sfm

## OpenCV Installation

```
 sudo apt-get install build-essential
 sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
 sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev```
 
cd ~/<my_working _directory>
git clone https://github.com/opencv/opencv.git
git clone https://github.com/opencv/opencv_contrib.git
```
Now, delete the sfm folder from opencv_contrib folder.

```
cmake [<some optional parameters>] <path to the OpenCV source directory>
```
For example:
```
cd ~/opencv
mkdir release
cd release
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D OPENCV_EXTRA_MODULES_PATH=<path to oencv_contrib/modules> <path to opencv source code>
make -j4
sudo make install
```
## SfM dependencies
```
sudo apt-get install libeigen3-dev libgflags-dev libgoogle-glog-dev

```
