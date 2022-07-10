# Linux Introduction
Linux之父：Linus Torvalds， 基于Minix开发，1991年9月0.01版本

# 1.Linux和Unix的关系
```
Multics：贝尔实验室，批处理，分时多用户（失败）   
Unix：Ken Thompson，Dennis Ritchle（成功）
语言：汇编 ——> B语言（new B）（C语言）  
```
Unix发行版：
```
Solaris(Sun公司，被Oracle收购了，主要运行在高性能主机上，受限)    
IBM-AIX  
HP-UX  
BSD(FreeBSD协议，可以用开源代码开发闭源代码) ——> Darwin(MACOS) 
```   
```
Minix:一个不带任何unix版权的操作系统，借鉴了unix的思想  
Minix——>Linux
GNU——>GNU's not Unix(GPL协议，源码完全公开，使用后也必须公开)
```

__操作系统层级结构概念：__  
```
外围应用层（--命令解释层（shell）（-- 硬件接口层（核心层kernel，狭义的linux）（--计算机硬件  
```
Linux发行版：  
```
RedHat（性能强悍稳定）：RHEL，fedora，CentOS，deepin
debian（最遵循GNU规范的）：Ubuntu（基于debian不稳定版本开发的，有最新的软件包）  
SUSE（GUI漂亮）：OpenSUSE  
gentoo（性能最强悍，直接编译源码包安装操作系统）  
archlinux（轻量，灵活，滚动更新）：manjaro  
```

# 2.Linux安装
__使用版本：CentOS7__  
```
下载方式：www.centos.org -> downloads -> centos7 -> RPMs -> isos/ -> x86_64  
安装虚拟机：......
配置虚拟机系统：自定义 -> 处理器内核总数<实体主机逻辑处理器数目 -> I/O控制器（LSI logic） ->  虚拟磁盘类型SCSI  ->  虚拟磁盘拆分成多个文件  
安装系统：安装源：本地介质，软件选择：GNOME桌面  ->  磁盘分区：标准分区，/boot 1G xfs，swap 4G swap， / 45G xfs  -> 禁用Kdump
```
# Linux Desktop & terminal
```
Ctrl+Alt+F2-6：打开无图形化界面终端
Ctrl+Alt+F1：打开图形化桌面
Ctrl+Shift+=：放大terminal界面
Ctrl+Shift+-：缩小terminal界面
```