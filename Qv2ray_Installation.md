# Qv2ray Installtion / proxychains 
系统： _Ubuntu 20.04 TLS_
## 安装Qv2ray Client
1. 通过Ubuntu Store来直接安装：  
Ubuntu Store -> search -> Qv2ray -> installation

2. 或者从github上下载AppImage：  
https://github.com/Qv2ray/Qv2ray -> Release -> Qv2ray.v2.6.3.linux-x64.AppImage


## 下载v2ray core 
1. https://github.com/v2fly/v2ray-core -> Release -> v2ray-linux-64.zip
2. 解压到指定目录

## Qv2ray setting
1. Qv2ray -> 首选项 -> 内核设置 -> V2Ray核心可执行文件路径 / V2Ray 资源目录
2. 分组 -> 订阅设置 -> 设置订阅地址 -> 更新订阅

## 安装proxychains 
1. apt-get install proxychains  
2. sudo gedit /etc/proxychains.conf -> 最后一行添加 
```
socks4 ip:端口
socks5 ip:端口
打开dynamic 关闭static 
```

3. 使用proxychains 启动应用程序
```
例如：proxychains apt-get upgrade
```