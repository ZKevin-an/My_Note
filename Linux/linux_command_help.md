# shell简介
```
命令解释器，shell解释输入的命令并交给内核执行
历史：基于Unix的Bourne Shell（很好的运行脚本，但是解释命令较慢），到linux成为Bourne Again Shell ——> bash（较臃肿和复杂）
PS：Ubuntu使用的是dash,在bin文件夹中可见sh链接到bash
```

# 内嵌命令和外部命令
```
内嵌命令：存在于bash源码内的（build-in）
外部命令：除内置命令外的命令
type 命令  # 判断是内置命令还是外部命令
```

# man(manual)
```
man 命令（看不了内置命令）
man -f 命令（看内置命令说明）
```

# help\--help
```
help 命令（只能看内置命令的手册）
命令 --help 命令内置的手册说明（必须是外部命令）
```

# reset\clear\ctrl+c
```
clear\ctrl+c：清屏
reset：重启shell环境
```