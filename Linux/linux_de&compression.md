# gzip|gunzip
```
gzip 文件：压缩文件
gunzip 文件.gz：解压文件
Ps：只能压缩文件，不能压缩目录，压缩多个文件会出现多个压缩包
```

# zip|unzip
```
zip 选项 压缩包名称.zip  要压缩的内容
-r 压缩目录
unzip 选项 压缩包名称.zip
-d 指定解压后文件的存放目录
```

# tar
```
tar 选项 XXX.tar.gz 内容：打包目录，压缩后的文件格式.tar.gz
-c 产生.tar打包文件
-v 显示详细信息
-f 指定压缩后的文件名
-z 打包同时压缩
-x 解包.tar文件
-C 解压到指定目录
例子 tar -zcvf xxx.tar.gz XXX XXX XXX  # 压缩多个文件
例子 tar -zcvf xxx.tar.gz XXX # 压缩目录
例子 tar -zxvf xxx.tar.gz -C 指定目录 # 解压压缩包到指定目录
```