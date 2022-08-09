# 配置固定ip
```
1.vim /etc/sysconfig/network-scripts/ifcfg-网络适配器名称
2.修改BOOTPROTO为static
3.在文本最后添加：
    IPADDR=192.168.202.100
    GATEWAY=192.168.202.2
    DNS1=192.168.202.2
4.保存退出后，重启网络服务：service network restart
PS：注意设置的网络号必须与虚拟机网卡中设置网络号相同，虚拟机ping不通物理机，因为物理机防火墙的设置
```
# 修改主机名
```
vim /etc/hostname修改里面的内容即可
或者
hostnamectl set-hostname 主机名
```
# 修改hosts
```
vim /etc/hosts
根据格式直接添加即可
PS：windows电脑的hosts在C/windows/System32/drivers/etc下
```