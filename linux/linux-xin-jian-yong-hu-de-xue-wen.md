# Linux 新建用户的学问

# 背景

一般我们在阿里云买了一台服务器后

服务器只是有root这么一个可登陆用户

但是生产环境中, 我们千万不能试图用root用户去跑所有的程序

服务器要跑服务, 一般是要新建一个用户来承担这项工作

# 新建用户常规操作

```bash
# 新建用户组
> groupadd work
# 新建用户work, 并且加入到work用户组, 并指定家目录为/home/work
> useradd -g work -d /home/ work
# 指定用户shell 为/bin/bash
> usermod -s /bin/bash work
```

# 添加root的命令行设置到work

即使我们设置了work 的执行shell 为/bin/bash

我们会发现阿里云给我们创建的root

会有`ll`命令, `ls`的命令有高亮颜色等等

这是因为`/root/.bashrc`这个shell的功能, 里面对一些命令进行了alias

如果我们想要这些功能的话, 可以将这个文件复制到work用户中

```bash
> cp /root/.bashrc /home/work/.bashrc
> chown work:work /home/work/.bashrc
> source /home/work/bashrc
```

需要添加`~/.bash_profile`文件为每次ssh登录 `source ./bashrc`

```bash
$ vim ~/.bash_profile
```

内容

```bash
if test -f .bashrc ; then
  source .bashrc 
fi
```

# 新增用户work 用密钥进行ssh登录

阿里云服务器中, 创建服务器时, 可以选择root的ssh登录方式为密钥/密码 等方式

这里说下 对新增用户work, 用户设置为密钥进行ssh登录

> tips 假设对已设置密码的用户 可以将 /etc/shadow 文件中改用户的第二项字符改为*

本机shell(非服务器shell, 一般指用户自己的计算机)


```bash
> cd ~/.ssh
# 会创建 404mzk-serve-work 和 404mzk-serve-work.pub文件
> ssh-keygen -f 404mzk-serve-work
# 通过root的密钥 将公钥发放到到云服务器中, 切勿忘记最后的:
> scp -i /Users/maizhikun/.ssh/root密钥文件.pem  404mzk-serve-work.pub root@云服务器ip:

```

云服务器root-shell

```bash
# 将/root/404mzk-serve-work.pub 复制到work用户中
> mkdir /home/work/.ssh
> mv 404mzk-serve-work.pub /home/work/.ssh/ authorized_keys
```

# 用户work添加免密sudo

一般情况下 给work加sudo权限, work执行sudo命令的时候 还是需要确认密码的

但我们的work木有密码阿 怎么破, 执行以下命令即可

```bash
chmod u+w /etc/sudoers
echo "work       ALL=(ALL)       NOPASSWD: ALL" >> /etc/sudoers
chmod u-w /etc/sudoers
```

# github账号ssh设置

假设阿里云服务器要拉取github的仓库 则需要在github设置服务器的ssh-key

参考链接 https://help.github.com/en/github/authenticating-to-github/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent

执行的相关shell

```bash
# cd到~/.ssh并生成ssh-key 之后需要填写密钥名字和密码信息 ,密码信息可以直接回车 不设置密码
$ cd ~/.ssh & ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# 启动ssh-agent服务
$ eval "$(ssh-agent -s)"
# 添加密钥文件到ssh
$ ssh-add ~/.ssh/密钥名
```

笔者的服务器之前没有ssh-add这个命令 所以需要安装

```bash
# 具体参考 https://www.cyberciti.biz/faq/how-to-install-ssh-on-ubuntu-linux-using-apt-get/
$ sudo apt-get install openssh-client
```