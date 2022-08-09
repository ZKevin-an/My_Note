# linux启动流程
```
1.自检，检查硬件故障
2.如果有多块启动盘，需要在BIOS中选择启动磁盘
3.启动MBR中的bootloader引导程序
4.加载内核文件
5.执行所有进程的父进程，systemd
6.欢迎界面
加载内核时的关键文件：
1)kernel文件：vmlinuz-3.10.0-957.el7.x86_64
2)initrd文件：initramfs-3.10.0-956.el7.x86_64.img
```

# 制作mini linux思路分析
```
1.在现有系统上加一块硬盘/dev/sdb，在硬盘上分区/boot和/，并格式化。
2.在sdb上打造独立linux系统
3.要把内核文件initramfs拷贝的sdb上
4.自制linux完成，创建一个新的linux虚拟机，将硬盘指向创建的硬盘，启动即可
```

# 操作流程
```
1.先插入一块硬盘
2.通过lsblk查看是否插入成功，再通过fdisk进行分区
3.对分区进行格式化：
mkfs.ext4/dev/sdb1
mkfs.ext4/dev/sdb2
4.创建目录，挂载新硬盘
mkdir -p /mnt/boot /mnt/sysroot
mount /dev/sdb1 /mnt/boot
mount /dev/sdb2 /mnt/sysroot
5.安装grub，将内核文件拷贝至磁盘
grub2-install --root-directory=/mnt/dev/sdb # 安装grub2
hexdump -C -n 512 /dev/sdb  # 查看是否安装成功
cp -rf /boot/* /mnt/boot #拷贝内核文件
6.修改grub.cfg文件
vim /mnt/boot/grub2/grub.cfg  # 修改里面的UUID，并加上selinux=0 init=/bin/bash
7.创建目标主机根文件系统
mkdir -pv /mnt/sysroot/{etc/rc.d,usr,var,proc,sys,dev,lib,lib64,bin,sbin,boot,srv,mnt,media,home,root}
8.拷贝需要的bash和库文件给新的系统使用
cp /lib64/*.* /mnt/sysroot/lib64/
cp /bin/bash /mnt/sysroot/bin/
9.将磁盘单独启动即可
```

# 阅读内核源码
```
https://www.kernel.org/
建议从0.01入手
文件夹内容：
boot：和系统引导相关代码
fs：存放linux支持的文件系统代码
include：存放linux核心需要的头文件，比如asm，linux，sys
init：系统初始化相关代码
kernel：和系统内核相关的源码
lib：存放库代码
mm：内存管理相关代码
tool：工具代码
```

# 更新内核版本
```
yum info kernel -q //检测内核版本，显示可以升级的内核版本
uname -a //查看当前内核版本
yum update kernel //升级内核
yum list kernel -q //查看已经安装的内核
```