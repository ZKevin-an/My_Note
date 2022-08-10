# webmin
```
基于web的Unix/Linux系统管理工具
安装步骤：
1.下载地址：http://download.webmin.com/download/yum/
也可以用wget http://download.webmin.com/download/yum/webmin-1.700-1.noarch.rpm
2.安装：rpm -ivh webmin-1.700-1.noarch.rpm
3.重置密码：/usr/libexec/webmin/changepass.pl /etc/webmin root test #将webmin中的root用户密码改成test
4.修改webmin服务的端口号（默认10000，出于安全考虑）：
vim /etc/webmin/miniserv.conf
将port=10000修改为其他端口号
5.重启webmin
/etc/webmin/restart # 重启
/etc/webmin/start   # 启动
/etc/webmin/stop    # 停止
6.防火墙放开端口
firewall-cmd --zone=public --add-port=666/tcp --permanent # 配置防火墙开放6666端口
firewall-cmd --reload   # 更新防火墙配置
firewall-cmd --zone=public --list-ports # 查看已开放的端口号
7.登录webmin
http://ip:6666
```

# bt（宝塔）
```
安装：
yum install -y wget && wget -O install.sh http://download.bt.cn/install/install_6.0sh && sh install.sh
ps：要记录下给定的username和password，避免之后忘记，如果忘记密码了，可以使用bt default查看
```