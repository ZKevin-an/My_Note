# 安装dump和restore
```
yum -y install dump
yum -y install restore
```

# dump基本介绍
```
dump支持增量备份
参数说明：
-c：创建新的归档文件
-0123456789：备份的层级，0为最完整备份，会备份所有文件，后续的1到9都只备份新增或修改的文件
-f：指定备份后的文件名
-j：调用bzlib库压缩备份文件，压缩成bz2格式
-T：指定开始备份的时间和日期
-u：备份完毕后，在/etc/dumpdares中记录备份的文件系统，层级，日期和时间等
-t：指定备份文件，如果已经存在，则列出名称
-W：显示需要备份文件及其最后一次备份的层级和时间，日期
-w：与W相似，仅显示需要备份的文件
例子：
将/boot的所有内容备份到/opt/boot.bak.bz2中，层级为0：dump -0uj -f /opt/boot.bak.bz2 /boot
在/boot目录下拷贝一个文件，再次备份，层级为1：dump -1uj -f /opt/boot.bak1.bz2 /boot
显示需要备份的文件及其最后一次备份的层级，时间，日期：
dump -W
查看备份时间文件：
cat /etc/dumpdates
dump不支持对目录和文件的增量备份
```

# restore
```
恢复已备份的文件，可以使用四种模式，但是不能一起用：
-C：对比模式，将备份的文件与已存在的文件相互对比
-i：交互模式，还原操作时依序询问用户
-r：还原模式
-t：查看模式，看备份文件中有哪些文件
选项：
-f：从指定文件中读取备份数据
例子：
命令对比模式：
restore -C -f boot.bak1.bz2
对于增量备份的文件，需要依次将所有备份文件都还原，如：
restore -r -f /opt/boot.bak0.bz2
restore -r -f /opt/boot.bak1.bz2
restore -r -f /opt/boot.bak2.bz2
```