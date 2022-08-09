# cut
```
从文件的每一行剪切字节，字符和字段，并输出
cut 选项参数 filename
-f：列号
-d：分隔符，按照指定分隔符分割列，默认为“\t”
-c：按字符进行切割，加n代表第n列
```

# awk
```
文本分析工具，进行文本切片
用法：
awk 选项参数 '/pattern1/{action1} /pattern2/{action2}...' filename
pattern：查找的内容
action：匹配到时执行的命令
-F：指定输入文件分隔符
-v：赋值一个用户定义变量
```