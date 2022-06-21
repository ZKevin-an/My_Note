# GNURadio 3.8.2.0 Installation
系统： _Ubuntu 20.04 TLS_  
Version： _GNURadio 3.8.2.0_  
注：__GNURadio 3.9__ 版本不支持 __GR-lora__ 模块

# 1.安装依赖项
可见：https://wiki.gnuradio.org/index.php/UbuntuInstall#Focal_Fossa_.2820.04.29
```
sudo apt install git cmake g++ libboost-all-dev libgmp-dev swig python3-numpy \
python3-mako python3-sphinx python3-lxml doxygen libfftw3-dev \
libsdl1.2-dev libgsl-dev libqwt-qt5-dev libqt5opengl5-dev python3-pyqt5 \
liblog4cpp5-dev libzmq3-dev python3-yaml python3-click python3-click-plugins \
python3-zmq python3-scipy python3-gi python3-gi-cairo gobject-introspection gir1.2-gtk-3.0
```

# 2.安装Volk
VOLK是向量优化的内核库。它是一个包含用于不同数学运算的手写SIMD代码内核的库。由于每种SIMD体系结构都可能非常不同，并且还没有编译器能够正确或高效地处理矢量化，因此VOLK会以不同的方式解决此问题。
```
cd    //返回主目录
git clone --recursive https://github.com/gnuradio/volk.git  //循环克隆git子项目（由于我的cpu用的是AMD的，有些CPU特性需要修改，需要参考README中的missing submodule）
cd volk
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DPYTHON_EXECUTABLE=/usr/bin/python3 ../
make
make test
sudo make install
sudo ldconfig
```

# 3.安装GNU Radio 3.8版本
```
cd
git clone -b maint-3.8 https://github.com/gnuradio/gnuradio.git //选择3.8.2.0版本
cd gnuradio
git submodule update --init --recursive  
mkdir build
cd build
cmake -DCMAKE_BUILD_TYPE=Release -DPYTHON_EXECUTABLE=/usr/bin/python3 ../
make
make test
sudo make install
sudo ldconfig
```

# 4.添加 PYTHONPATH 和 LD_LIBRARY_PATH
(1).找到安装目录：gnuradio-config-info --prefix  
(2).找到python library：find {your-prefix} -name gnuradio | grep "packages"  
(3).添加路径到bash start-up file：  
sudo gedit ~/.bashrc 在文件的末尾添加：  
```
export PYTHONPATH={your-prefix}/lib/{Py-version}/dist-packages:$PYTHONPATH  
export LD_LIBRARY_PATH={your-prefix}/lib:$LD_LIBRARY_PATH
```

# 5.检查
GNU Radio运行：gnuradio-companion  
GNU Radio版本：gnuradio-config-info --version
