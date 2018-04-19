# 常用知识

# 服务器强制覆盖本地git

```git
git fetch --all  
git reset --hard origin/master
```

# `push`代码时只提示输入密码

```git
404-K:*** maizhikun$ git push origin master
git@***.net's password:
```

拉代码时,只提示要输入密码,其实这个是要ssh进入服务器的密码,因为代码的ssh没配置好

# `fatal: protocol error: bad line length character: No s`

因为ssh在项目中配置了只读.

```git
404-K:*** maizhikun$ git push origin master
fatal: protocol error: bad line length character: No s
```

配置好ssh就行

# git clone 时想重命名

git clone 时,默认为仓库的名字 要想重命名 砸破

`git clone 地址 重命名称`

# Permission denied (publickey).后常用的命令

1. 查看当前有效的ssh : `ssh-add -l`
      
      2048 96:32:2a:5d:5c:c1:74:25:1a:d2:d4:39:76:ec:66:6e /Users/maizhikun/.ssh/id_rsa (RSA)
2. 尝试能否连接git服务商 : `ssh -T git@github.com`
3. 重新生成key : `ssh-keygen`

# git查看单人提交的commit

普通调用

`git log --author "单人名字"`

参考链接还有一些正则的使用方式

参考<http://stackoverflow.com/questions/4259996/how-can-i-view-a-git-log-of-just-one-users-commits>

# 查看文件修改记录

> 1. git log -p filename

能详细显示出每次该文件修改的diff

> 2. git show commit号 filename

 能查看该次commit的文件修改
 
> 3. git log filename 或 git log --pretty=oneline filename

 显示出与该文件有关的commit
 
 # 修改分支名称
 
 > 本地分支重命名
 
 git branch -m oldName newName
 
 > 远程分支重命名
 
 1. 修改远程分支中的本地分支: git branch -m oldName newName
 2. 删除远程分支: git push --delete origin oldName
 3. 重新上传本地分支: git push origin newName
 
 参考链接: https://www.jianshu.com/p/cc740394faf5
   

