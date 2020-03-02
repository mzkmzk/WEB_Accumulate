# Linux 新建用户的学问

# 背景

一般我们在阿里云买了一台服务器后

服务器只是有root这么一个可登陆用户

但是生产环境中, 我们千万不能视图用root用户去跑所有的程序

服务器要跑服务, 一般是要新建一个用户来承担这项工作

# 新建用户常规操作

```bash
# 新建用户组
> groupadd work
# 新建用户work, 并且加入到work用户组, 并指定家目录为/home/work
> useradd -g work work -d /home/work
# 指定用户shell 为/bin/bash
> usermod -s /bin/bash work
```

# 添加root的命令行设置到work

即使我们这是了work 的执行shell 为/bin/bash

我们会发现阿里云给我们创建的root

会有`ll`命令, `ls`的命令有高亮颜色等等

这是因为`/root/.bashrc`这个shell的功能, 里面对一些命令进行了alias

如果我们想要这些功能的话, 可以将这个文件复制到work用户中

```bash
> cp /root/.bashrc /home/work/.bashrc
> chown work:work /home/work/.bashrc
> source /home/work/bashrc
```

# 新增用户work 用密钥进行ssh登录

阿里云服务器中, 创建服务器时, 可以选择root的ssh登录方式为密钥/密码 等方式

这里说下 对新增用户work, 用户设置为密钥进行ssh登录

> tips 假设对已设置密码的用户 可以将 /etc/shadow 文件中改用户的第二项字符改为*

本机shell(非服务器shell, 一般指用户自己的计算机)

```bash
> cd ~/.ssh
# 会创建 404mzk-serve-work 和 404mzk-serve-work.pub文件
> ssh-keygen -f 404mzk-serve-work
# 通过root的密钥 将公钥发放到到云服务器中, 切勿忘记最后的ip
scp -i /Users/maizhikun/.ssh/root密钥文件.pem  404mzk-serve-work.pub root@云服务器ip:
```

云服务器root-shell

```bash
# 将/root/404mzk-serve-work.pub 复制到work用户中
> mkdir /home/work/.ssh
> mv 404mzk-serve-work.pub /home/work/.ssh/ authorized_keys
```
