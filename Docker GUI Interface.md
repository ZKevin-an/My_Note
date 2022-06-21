# Docker GUI Interface
系统： _Ubuntu 20.04 LTS_  
依赖项： _Docker_
# 开放权限，允许访问x11显示接口
1. sudo apt-get install x11-xserver-utils
2. xhost +（注：需要在每次开始前都进行一次xhost +）   

# 查看docker镜像，找到镜像的name和tag
```
docker images  //查看所有镜像
```

# 启动docker容器
```
sudo docker run -it -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=unix$DISPLAY -e GDK_SCALE -e GDK_DPI_SCALE rpp0/gr-lora（镜像name）:latest（镜像tag） /bin/bash

-it   确保容器具有与其关联的有效tty并且stdin保持连接状态。（正在尝试运行bash，它是一个需要tty才能运行的交互式外壳）  
-v /tmp/.X11-unix:/tmp/.X11-unix      //共享本地unix端口  
-e DISPLAY=unix$DISPLAY               //修改环境变量DISPLAY  
-e GDK_SCALE                          //我觉得这两个是与显示效果相关的环境变量，没有细究  
-e GDK_DPI_SCALE 
```

# 运行容器
```
docker start [容器名]
docker attach [容器名]
```