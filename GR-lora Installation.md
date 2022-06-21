# GR-lora Installation
系统： _Ubuntu 20.04 TLS_   
依赖项：GNU Radio 3.8.2.0   
Version： _rpp0/gr-lora 0.6.2_  
 
# 安装liquid-dsp：
https://github.com/jgaeddert/liquid-dsp
```
git clone git://github.com/jgaeddert/liquid-dsp.git
./bootstrap.sh
./configure
make
sudo make install
sudo ldconfig
```

# 安装gr-osmocom
```
git clone -b v0.2.2 git://git.osmocom.org/gr-osmosdr
cd gr-osmosdr/
mkdir build
cd build/
cmake ../
make
sudo make install
sudo ldconfig
```

# 安装gr-lora
```
git clone https://github.com/rpp0/gr-lora.git
mkdir build
cd build
cmake ../  # Note to Arch Linux users: add "-DCMAKE_INSTALL_PREFIX=/usr"
make
make test
sudo make install
sudo ldconfig
//由于之前安装GNU Radio的时候已经添加好了 PYTHONPATH 和 LD_LIBRARY_PATH 路径，所以不需要再添加路径了
```

# 安装rtl-sdr驱动
1. 可能的更新：
```
sudo apt-get update
```
2. 安装需要的工具:git,cmake,build-essential
```
sudo apt-get install git
sudo apt-get install cmake
sudo apt-get install build-essential
```
3. 安装用于接入USB设备的通用C库：libusb-1.0-0-dev
```
sudo apt-get install libusb-1.0-0-dev
```
4. 获取并编译构建 RTL2832U Osmocom 驱动
```
git clone git://git.osmocom.org/rtl-sdr.git
cd rtl-sdr/
mkdir build
cd build
cmake ../ -DINSTALL_UDEV_RULES=ON
(出现问题：--Could NOT find PkgConfig(missing:PKG_CONFIG_EXECUTABLE)
尝试：sudo apt-get install --reinstall pkg-config cmake-data)
make
sudo make install 
sudo ldconfig
sudo cp ../rtl-sdr.rules /etc/udev/rules.d/
```
5. 将可能导致冲突的驱动列入黑名单
```
cd /etc/modprobe.d/
sudo gedit blacklist-rtl.conf
(添加一行：blacklist dvb_usb_rtl28xxu并保存)
```
6. 测试驱动
```
rtl_test -t
```
