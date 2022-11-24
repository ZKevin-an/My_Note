# 安装GNURadio 
详见 GNURadio_3.8.2.0_installation.md

# 安装更新pip
```
sudo apt install python3-pip
sudo pip install -U pip
```

# 安装pybombs
```
sudo pip install pybombs
```

# 获取安装库
```
pybombs recipes add gr-recipes git+https://github.com/gnuradio/gr-recipes.git
pybombs recipes add gr-etcetera git+https://github.com/gnuradio/gr-etcetera.git
```

# 安装相关库
```
sudo apt-get install gqrx-sdr
sudo pybombs install hackrf rtl-sdr gr-osmosdr osmo-sdr
注：其中安装到gnuradio部分能跳过（到了gnuradio部分已经够了）
```

# 下载uhd镜像文件
```
sudo uhd_images_downloader
```

# 安装gr-lora_sdr
```
git clone https://github.com/jkadbear/gr-lora.git
mkdir build
cd build
cmake ../
make
sudo make install
sudo ldconfig
```