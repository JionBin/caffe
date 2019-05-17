<!-- #AG_DEMOAPP_HEADER_BEGIN# -->
### 安装Caffe+OpenCV4.0.1 Ubuntu16.04


<!-- #AG_DEMOAPP_HEADER_END# -->
<!-- #AG_BRIEF_BEGIN# -->
1. 安装依赖库：
```
sudo apt-get install -y --no-install-recommends libboost-all-dev
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libboost-all-dev libhdf5-serial-dev libgflags-dev libgoogle-glog-dev liblmdb-dev protobuf-compiler
```
2.  下载caffe源码

```
git clone https://github.com/BVLC/caffe
cd caffe
cp Makefile.config.example Makefile.config
```

3. Python
```
sudo pip install scikit-image protobuf
cd python
for req in $(cat requirements.txt); do sudo pip install $req; done
```
4. 本地电脑上没有N卡，所以装了CPU-ONLY版的，配置Makefile

```
cp Makefile.config.example Makefile.config
sudo vim Makefile.config

# CPU-only switch (uncomment to build without GPU support).
CPU_ONLY := 1
...
# Uncomment if you're using OpenCV 3
OPENCV_VERSION := 4
...
# ANACONDA_HOME := $(HOME)/anaconda2
...
# PYTHON_LIB := $(ANACONDA_HOME)/lib
...
 USE_PKG_CONFIG := 1
```
5. 编译

```
make all
```
在编译的时候可能遇到的错误：
a. `fatal error: hdf5.h: No such file or directory`
找不到`hdf5`的头文件，将其路径添加到Makefile即可。

```
vim Makefile.config
INCLUDE_DIRS := $(PYTHON_INCLUDE) /usr/local/include /usr/include/hdf5/serial/
LIBRARY_DIRS := $(PYTHON_LIB) /usr/local/lib /usr/lib /usr/lib/x86_64-linux-gnu/hdf5/serial/
```
可能还会出现找不到`hdf5`的链接问题，可以用如下方法关联链接

```
ln -s /usr/lib/x86_64-linux-gnu/libhdf5_serial.so.10.1.0 /usr/lib/x86_64-linux-gnu/libhdf5.so
sudo ln -s /usr/lib/x86_64-linux-gnu/libhdf5_serial_hl.so.10.0.2 /usr/lib/x86_64-linux-gnu/libhdf5_hl.so
```
b. OpenCV4.0.1  `-std=c++11 or -std=gnu++11`问题
在caffe的默认`Makefile`中没有enable `-std=c++11`，在`Makefile`中使能即可

```
vim Makefile
COMMON_FLAGS += -DNDEBUG -O2 -std=c++11
```
c. OpenCV `CV_LOAD_IMAGE_COLOR CV_LOAD_IMAGE_GRAYSCALE`错误
在OCV4.0.1中，其对应的为`cv::IMREAD_COLOR cv::IMREAD_GRAYSCALE`，把源码中的对应的宏替换下即可

6. 测试

```
make all
make test
make runtest
make pycaffe 
```
`caffe::CPUDevice<float>(0 ms)` 错误

```
export LC_ALL="C"
```
```
import caffe 出现“syntax”错误
这个错误往往是由于protobuf的版本问题，把版本切换为2.6.1就没问题了
```
```
sudo pip uninstall protobuf
再重装v2.6.1
```
        
<!-- #AG_BRIEF_END# -->
