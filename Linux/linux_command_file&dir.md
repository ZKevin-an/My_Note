# pwd
```
print working directory 打印目前工作目录的绝对路径（内嵌命令）
```

# cd
```
cd - 返回跳转前的目录
```

# ls(list)
```
ls 参数 目录|文件
-a 全部文件，包括隐藏文件和文件夹
-l 长数据串列出，相当于ll
```

# mkdir
```
创建文件夹
-p（parent） 如果没有父目录，则创建父目录
```

# rmdir
```
删除文件夹
-p（parent） 如果父目录为空，则删除父目录
```

# touch
```
创建文件  Ps：vim不能创建一个空文件，但是touch能
```

# cp
```
cp 参数 source dest  复制文件或者文件夹
-r（recursion） 复制整个文件夹
Ps：\cp中的\表示不使用别名
```

# rm(remove)
```
rm 参数 deleteFile
-r（recursion） 递归删除目录中所有内容
-f（force） 强制执行，不需要提示确认
-v 显示指令的详细执行过程
```

# mv(move)
```
move old new 移动文件或文件夹 Ps：可以用来重命名文件
```

# cat(catch)
```
cat 参数 文件名
-n 显示行号
```

# more
```
more是基于vi的文本过滤器，以全屏的方式按页显示文本文件的内容
more 文件
space：翻页
Enter：翻行
q：退出
```

# less
```
比more更强大，支持各种终端显示，不是将文件全部加载后显示，而是只加载需要显示的内容，对于大型文件具有较高效率
```