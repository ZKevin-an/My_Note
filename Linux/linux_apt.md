# 更新apt镜像源（Ubuntu）
```
1.找到国内清华镜像源网站
https://mirrors.tuna.tsinghua.edu.cn/
找到对应系统，点击问号进入
2.备份Ubuntu默认地址
sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
3.清空sources.list文件内容
sudo echo '' > sources.list
4.将拷贝的内容填入文件中
5.更新源
sudo apt-get update
```