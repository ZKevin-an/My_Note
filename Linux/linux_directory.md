# 系统目录结构
挂载点：设置磁盘挂载到某一个文件夹下  
根目录下各个文件夹的功能：
```
bin：存放常见可执行命令，链接指向usr/bin
sbin：系统级的可执行命令。root可使用的命令，链接指向usr/sbin
lib：系统所需要的共享库文件，相当于win中的dll
lib64：64位相关的库文件
boot：系统启动引导内容
dev：硬件设备管理文件
etc：系统管理所需要的配置文件和其子目录
home：每个用户子目录的存放点
root：系统管理员（root用户）的主目录
opt：可选目录，给第三方软件提供的位置，第三方软件安装位置
media：识别一些可移动媒体设备（u盘，光驱）
mnt：移动化存储设备的另一个挂载点，和media相似
proc：进程目录，存放进程的映射
run：存放到目前为止的所有运行信息
srv：存放和系统服务相关的内容
sys：系统硬件信息相关的文件
tmp：临时目录，随意存放
usr：存放了应用程序和用户相关的数据和文件
var：可变目录，经常会修改的内容（日志等）
```