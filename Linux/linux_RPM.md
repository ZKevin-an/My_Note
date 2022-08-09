# RPM概述
```
RedHat Package Manager：RedHat软件包管理工具
RPM包的名称：软件名称+版本号+软件运行平台+文件扩展名（rpm）
debian——apt
RedHat——RPM
rpm -qa：查询所安装的所有rpm软件包
rpm -qi 软件名：展示详细信息
rpm -e rpm软件包：卸载软件包
rpm -e --nodeps：卸载软件时不检查依赖，那些使用该软件包的软件就不能正常运行了
rpm -ivh RPM 包全名：安装软件
-i install
-v 显示详细信息
-h hash进度条
--nodeps 安装前不检查依赖
Ps：光盘中存在很多rpm软件包，位于软件挂载目录下的Packages下
Ps：安装过程中遇到依赖没有安装会报错，需要自己手动安装依赖，所以需要yum
```

# YUM
```
Yellow dog Updater，一个在Fedora，CentOS和RedHat中的shell前端软件包管理器，基于RPM包管理，可以自动从服务器中下载RPM包并安装，自动处理依赖性关系
yum 选项 参数
参数：
-y 对所有回答都yes
选项：
install 安装
update 更新
check-update 检查是否有可用更新的rpm包
remove 删除指定的rpm包
list 显示软件包信息
clean 清理yum过期的缓存
deplist 显示yum软件包的所有依赖关系
```

# 修改网络YUM源
```
默认系统YUM源需要链接国外apache网站，网速慢
安装wget，wget用来从指定的URL下载文件：yum install wget
在/etc/yum.repos.d/目录下备份默认的repos文件：cp CentOS-Base.repo CentOS-Base.repo.backup
下载需要软件源的repo文件：wget https://mirrors.aliyun.com/repo/Centos-7.repo  或者 wget https://mirrors.163.com/.help/CentOS7-Base-163.repo
用下载好的repo文件替换默认repos文件：mv CentOS7-Base-163.repo CentOS-Base.repo
清理旧缓存数据，缓存新数据：
yum clean all
yum makecache：把服务器包信息下载到本地缓存起来
Ps：其实目前已经会从最近的服务器中下载软件，不需要手动去更换软件源
```