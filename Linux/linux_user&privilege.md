__Ps：__ 用户权限设置管理需要用到root用户
# root用户
```
对于CentOS7系统来说，创建时可直接创建root用户
而对于Ubuntu来说，安装后都是普通用户权限，没有root权限，所以一般使用su或者sudo获得root权限
一开始直接su会提示su：Authentication failure的问题，需要先给root设置密码：
sudo passwd
```

# useradd
```
useradd 用户名：添加新用户
useradd -g 组名 用户名：添加新用户到某个组
useradd -d 目录名称 用户名：添加新用户并设置主目录位置
```

# passwd
```
passwd 用户名：设置用户密码
```

# id
```
id 用户名：查看是否存在该用户账号，并显示用户信息
id：查询正在使用的用户账号
/etc/passwd：可以查看当前所用的用户信息，也可以看到系统用户信息
```

# su(switch user)
```
su 用户名：切换用户
```

# who am i|whoami
```
查询当前用户
whoami只会查询当前窗口的使用用户，who am i会查询真正的使用者
```

# sudo
```
sudo 用户名：赋予普通用户管理员权限
需要先将该用户加入sudoers里面：
vim /etc/sudoers
添加一行：用户名  ALL=(ALL)  ALL|NOPASSWD:ALL(不用输入密码)
```

# userdel
```
删除用户，但是会保留该用户的主目录
-d：删除用户同时删除主目录
```

# groupadd
```
groupadd 组名：添加一个用户组
/etc/group：可以查看系统下的所用用户组，包括系统用户组
Ps：wheel组类似于管理员组
```

# usermod 
```
修改用户
usermod -g 用户组 用户：修改用户的用户组
```

# groupmod
```
修改用户组
groupmod -n 新用户组名 原用户组名：修改用户组名
```

# groupdel
```
groupdel 用户组名：删除用户组
```

# 文件属性
```
通过ls -l可以阅读文件属性
         文件类型  属主权限   属组权限   其他用户权限
十个字符串   0     1  2  3    4  5  6     7  8  9
          d 目录   读写执行    读写执行    读写执行
          l 链接
          c 字符设备（鼠标键盘）
          d 块设备（硬盘）
          - 文件
对于文件的解释：对于文件，写权限不代表能够删除文件，需要有对目录具有写权限才行
               对于目录，可执行权限代表能够进入目录内
```

# chmod
```
改变权限
第一种方式 chmod [{ugpa}{+-=}{rwx}] 文件|目录， u属主，g属组，o其他人，a所有人
第二种方式 chmod [421] 文件|目录，4=读，2=写，1=执行
-R 修改整个文件夹里面所有文件的权限
```

# chown
```
改变属主
chown 选项 最终用户 文件|目录
-R：递归操作
```

# chgrp
```
chgrp 最终用户组 文件|目录
```