# rsync实践

# 简介

通过rsync算法 使本地和远程主机之间文件达成同步, 只传送不同的部分

# rsync daemon

这是一个在被传输服务器设置传输配置, 而传输服务器只需要设置传输配置的名称进行传输

### 被传输服务器设置配置文件

rsyncd.conf

```
# uid 作为复制rsync的uid 需要保证对被传输path有读写权限
uid = work
# gid 作为复制rsync的gid
gid = work
use chroot = no
timeout = 600
max connections = 400
# 保证有目录即可 文件为自动生成
pid file = /etc/rsync/test-rsync/rsyncd.pid
lock file = /etc/rsync/test-rsync/rsync.lock
log file = /etc/rsync/test-rsync/rsyncd.log

[test-rsync]
        path = /home/work/test-rsync
        refuse options = delete
        comment = test-rsync-comment
        read only = no
        #ignore errors
        auth users = mzkrsync
        secrets file = /etc/rsync/rsync.secrets
```

### 指定传输模块的账号密码 rsync.secrets

这个密码其实只是自己生成的密钥, 跟系统用户无关

在rsyncd.conf上面设置的secrets file路径 创建账号密码

格式为用户名:密码 格式名字要跟`auth users`对应

```
mzkrsync:abcdefg
```

tips: 该文件的属主要为root, 并且权限要为600

### 启动daemon

```
$ sudo rsync --daemon --config=/etc/rsync/rsyncd.conf
```

### 传输服务器执行传输命令

```
$ echo 'abcdefg' > ./ci.secret
$ chmod 600 ./ci.secret
$ rsync  -azP ./test-dir mzkrsync@47.115.81.11::test-rsync  --password-file=./ci.secret
building file list ...
2 files to consider

sent 100 bytes  received 16 bytes  77.33 bytes/sec
total size is 6  speedup is 0.05
```

到这里传输完毕

### 途中遇到的错误

- rsync: failed to connect to 47.115.81.11: Connection refused (61)

传输服务执行完rsync 就报这个错误

是因为 被传输服务器 启动rsync --daemon 没用root启动

- @ERROR: auth failed on module test-rsync

这是因为被传输文件的`rsync.secrets`的属主非root



# 参考链接

- https://www.samba.org/ftp/rsync/rsync.html
- rsync daemon详细配置说明: https://wsgzao.github.io/post/rsync/