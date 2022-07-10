# 介绍
__进程（process）：__ 一个正在执行的程序或命令  
__服务（service）：__ 启动后一直存在、常驻内存的进程  
__守护进程（daemon）：__ 守护进程维护系统服务

# service服务管理（CentOS 6）
```
service 服务名 start|stop|restart|status
查看服务：ls /etc/init.d
PS：对于CentOS 7来说，只保留了两个服务 network和netconsole
```

# systemctl
```
systemctl start|stop|restart|status 服务名
查看服务的方法:ls /usr/lib/systemd/system
```

# 系统服务配置
终端直接敲入”setup“

# 系统运行级别(runlevel)
```
系统开机启动步骤：（CentOS 6）
开机 -> BIOS -> /boot -> init进程 -> 运行级别 -> 运行级对应的服务
查看默认级别：vim /etc/inittab
Linux系统共有7种运行级别：
runlevel 0: 系统停机状态，系统默认runlevel不能为0，否则无法正常启动
runlevel 1：单用户工作状态，root权限，用于系统维护，禁止远程登录（进入无需输入密码即为root用户，可以通过这种模式更改root密码）
runlevel 2：多用户工作状态（无NFS），不支持网络
runlevel 3：完全多用户状态（有NFS），登陆后进入控制台命令模式（无GUI）
runlevel 4：系统未使用，保留
runlevel 5：X11控制台，登录后进入GUI模式
runlevel 6：系统正常关闭并重启，默认运行级别不能为6，否则无法正常启动

CentOS 7进行了简化
multi-user.target 等价于runlevel 3（多用户有网，无图形界面）
graphical.target 等价于runlevel 5（多用户有网，有图形界面）

查看当前运行级别： systemctl get-default
修改当前运行级别： systemctl set-default multi-user|graphical.target
```

# 检测服务在不同runlevel下的启动状态
```
老版本（只能看到init.d的服务）：
chkconfig --list
chkconfig --level 级别 服务 on|off
新版本（systemd）：
systemctl list-unit-files
systemctl disable|enable 服务  # 设置开机是否自启动
```