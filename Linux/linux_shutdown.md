# shutdown
```
shutdown 默认一分钟后关机
shutdown -c 取消关机命令
shutdown now 立刻关机
shutdown 数字 数字分钟后关机
shutdown time 在time时刻关机
PS：shutdown不立刻关机的原因————需要先进行sync操作（将内存同步到硬盘中），shutdown now也会进行sync操作
```

# halt
```
停机，关闭系统，但不断电（可以保持内存内容不丢失） == shutdown -H now
```

# poweroff
```
关机，断电 == shutdown -p now
```

# reboot
```
重启 == shutdown -r now
```