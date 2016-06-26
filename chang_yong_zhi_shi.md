# 常用知识

#1. 服务器强制覆盖本地git

```git
git fetch --all  
git reset --hard origin/master
```

#2 `push`代码时只提示输入密码

```git
404-K:*** maizhikun$ git push origin master
git@***.net's password:
```

拉代码时,只提示要输入密码,其实这个是要ssh进入服务器的密码,因为代码的ssh没配置好

# 3. `fatal: protocol error: bad line length character: No s`

因为ssh在项目中配置了只读.

```git
404-K:*** maizhikun$ git push origin master
fatal: protocol error: bad line length character: No s
```

配置好ssh就行

# 4. git clone 时想重命名

git clone 时,默认为仓库的名字 要想重命名 砸破

`git clone 地址 重命名称`

# 5. Permission denied (publickey).后常用的命令

1. 查看当前有效的ssh : `ssh-add -l`
      
      2048 96:32:2a:5d:5c:c1:74:25:1a:d2:d4:39:76:ec:66:6e /Users/maizhikun/.ssh/id_rsa (RSA)
2. 尝试能否连接git服务商 : `ssh -T git@github.com`
3. 重新生成key : `ssh-keygen`