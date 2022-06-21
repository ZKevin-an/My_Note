# Git Installtion
系统： _windows10_

# 1.Github创建仓库
# 2.安装Git
下载  -> 点点点
# 3.配置Git
1. ssh-keygen –t rsa –C “邮箱地址”  
后续回车就行，使用默认密码和默认保存路径（C/Users/xx/.ssh/id_rsa）
2. 找到id_rsa.pub并复制其内容
3. Github头像 -> Settings -> SSH keys -> Add SSH key ->将刚才复制的内容放入 -> Add key
4. ssh –T git@github.com  验证是否设置成功
5. 配置用户名和邮箱：
```
git config –global user.name “用户名”
git config –global user.email “邮箱”
```
# 4.托管项目
1. 创建一个目录
2. 右击目录 -> Git Bash Here
3. git与仓库的初始化
```
git init     //初始化
git remote add origin git@github.com:[用户名]/[仓库名].git   //添加一个新的远程仓库
git pull git@github.com:[用户名]/[仓库名].git   //本地与仓库同步
```
4. 上传文件
```
git add .
git commit -m "要写的内容"
git push git@github.com:[用户名]/[仓库名].git
```