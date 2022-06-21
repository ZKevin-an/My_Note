# Git Tutorial
系统： _windows10_

# 1.Git介绍
__免费的__，__开源的__ _分布式版本控制系统_  
官网：[git-scm.com](https://git-scm.com)  
作者：Linus
# 2.基本概念
工作区 ——> 暂存区(add) ——> 本地库(commit) ——> 远程库(push)

# 3.用户签名和初始化本地库，查看版本信息，版本窜梭
```
# 用于对每次修改签名，没有实际作用
git config --global user.name 用户名
git config --global user.email 邮箱
```
```
git init                # 初始化本地库，生成.git文件夹
git status              # 查看目前本地库状态，看是否有新增没有加入暂存区或者本地库
git reflog              # 查看版本简略信息
git log                 # 查看版本详细信息
git reset --hard 版本号 # 版本穿梭
```
# 4.添加暂存区，提交本地库
```
git add 文件名              # 添加文件到暂存区
git add .                  # 添加所有修改项到暂存区
git commit -m "日志信息"    # 提交到本地库
```
# 5.分支操作
```
git branch -v        # 查看分支
git branch 分支名    # 创建分支
git checkout 分支名  # 切换分支
git merge 分支名     # 把指定分支合并到当前分支上
```
# 6.远程库操作
```
git remote -v                # 查看当前所有远程地址别名
git remote add 别名 远程地址  # 添加别名，本地库别名之间不共享
git push 别名 分支            # 推送本地分支到远程库
git pull 别名 分支            # 拉去远程库到本地库
git clone 远程地址            # 克隆远程仓库到本地
```
# 7.SSH免密登录
```
1. ssh-keygen –t rsa –C 邮箱地址  
后续回车就行，使用默认密码和默认保存路径（C/Users/xx/.ssh/id_rsa）
2. 找到id_rsa.pub并复制其内容
3. Github头像 -> Settings -> SSH keys -> Add SSH key -> 将刚才复制的内容放入 -> Add key
4. ssh –T git@github.com  验证是否设置成功
5. 配置用户名和邮箱：
```
