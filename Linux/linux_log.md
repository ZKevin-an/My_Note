# log
```
系统日志放置于/var/log/文件夹下，其中存在的文件有：
boot.log：系统启动日志
cron：系统定时任务相关日志
cups/：打印信息日志
dmesg：开机时内核自检信总，可以使用dmesg查看内核自检信息
btmp：错误登录文件，二进制文件无法直接查看，要使用lastb（专门用于查看错误登录日志）
lasllog：所有用户最后一次登录时间的日志，也是二进制，专门的lastlog命令查看
mailog：邮件信息日志
message：系统重要信息日志，记录linux中绝大多数重要信息，如果系统出问题，首先要检查这个日志
secure：记录验证和授权方面的信息，设计账户和密码的程序都会记录，如系统登录，ssh登录，su，sudo，添加删除用户
wtmp：永久记录所有用户的登录、注销信息，同时记录系统的启动，重启，关机事件，二进制文件，要用last查看
ulmp：记录当前已经登录的用户信息，文件会随着用户登录和注销不断变化，只记录当前登录用户的信息，不能用vi查看，只能用who、uesrs查看
```

# 日志管理服务rsyslogd
```
CentOS7是rsyslogd，CentOS6是syslogd，rsyslogd功能更强大，并且兼容syslogd
查询rsyslogd服务是否启动：
ps aux | grep "rsyslog" | grep -v "grep"
查询rsyslogd服务的自启动状态
systemctl list-unit-files | grep rsyslog
日志配置文件：/etc/rsyslog.conf
编辑文件的格式：
*.*  存放日志文件
第一个*：日志类型，第二个*：日志级别
日志类型：
auth    pam产生的日志（为应用提供认证）
authpriv    ssh,ftp等登录信息的验证信息
corn    时间任务相关
kern    内核
lpr     打印
mail    邮件
mark(syslog)-rsyslogd   服务内部的信息，时间标识
news    新闻组
user    用户程序产生的相关信息
uucp    unix to unix copy主机之间的通信
local 1-7   自定义的日志设备
日志级别：
debug   有调试信息的，日志通信最多
info    一般信息日志，最常用
notice  最具有重要性的普通条件的信息
warning 警告级别
err     错误级别，阻止某个功能或者模块不正常工作的信息
crit    严重级别，阻止整个系统或者整个软件不能正常工作的信息
alert   需要立刻修改的信息
emerg   内核崩溃等重要信息
none    什么都不记录
日志文件的格式：
事件产生的时间
产生事件的服务器的主机名
产生事件的服务名或程序名
事件的具体信息
```

# 日志轮替
```
日志轮替：把旧日志文件移动并改名，并创建新的空日志文件，当旧日志文件超出一定范围时就会删除掉
/etc/logrotate.conf     全局的日志轮替策略/规则
/etc/logrotate.d/（推荐使用）        可以把某个日志的轮替规则写到该目录中，然后在conf文件中include
例如：
/var/log/wtmp {
    mothly
    create 0664 root utmp  # 建立新日志文件，权限0664 所有者是root 所属组是utmp
    minisize 1M
    rotate 1
}
ps:
0664最前面的0，是一个叫suid和guid和sticky的东西，将第一位0拆分成二进制的三部分：abc，
a - setuid位, 如果该位为1, 则表示设置setuid 对应4
b - setgid位, 如果该位为1, 则表示设置setgid 对应2
c - sticky位, 如果该位为1, 则表示设置sticky 对应1
setuid: 设置使文件在执行阶段具有文件所有者的权限. 典型的文件是 /usr/bin/passwd. 如果一般用户执行该文件, 则在执行过程中, 该文件可以获得root权限, 从而可以更改用户的密码.
setgid: 该权限只对目录有效. 目录被设置该位后, 任何用户在此目录下创建的文件都具有和该目录所属的组相同的组.
sticky bit: 该位可以理解为防删除位. 一个文件是否可以被某用户删除, 主要取决于该文件所属的组是否对该用户具有写权限.
如果没有写权限, 则这个目录下的所有文件都不能被删除, 同时也不能添加新的文件. 如果希望用户能够添加文件但同时不能删除文件,
则可以对文件使用sticky bit位. 设置该位后, 就算用户对目录具有写权限, 也不能删除该文件

日志文件参数说明：
conf文件编写规则：
daily                       每天轮替一次
weekly                      每周轮替一次
mothly
rotate 数字                 保留日志文件的个数
create mode owner group     轮替后创建新的日志文件
dateext                     使用时间最为轮替文件的后缀名
compress                    对轮替文件进行压缩
mail address                日志轮替时，输出内容通过邮件发送到指定邮箱
missingok                   日志不存在时，忽略日志的警告
notifempty                  日志为空时，不进行日志轮替
minisize 大小               日志轮替的最小值，一定要达到这个这个最小值才会轮替
size 大小                   日志只有在指定大小才进行日志轮替，而不是按照时间
sharedscripts               在此关键字之后的脚本只执行一次
prerotate/endscript         在日志轮替之前执行脚本命令
postrotate/endscript        在日志轮替之后执行脚本命令

日志轮替机制原理：
通过crond实现的，可以在/etc/crond.daily/中找到logrotate任务，说明系统每天会执行一次logrotate
```

# 内存日志
```
有些日志是放入内存中的，重启就会清空，和系统是实时相关的内容
通过journalctl查看内存日志，常用指令：
journalctl：# 查看全部
journalctl -n 3：# 查看最新的3条
--since 19:00 --until 19:10  # 查看从起始到结束时间的日志
-p err # 报错日志
-o verbose  # 日志详细内容
_PID=1234 _COMM=sshd  # 查看包含这些参数的日志
```