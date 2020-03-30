# Ubuntu 新服务器的设置 

# Docker的安装

https://docs.docker.com/install/linux/docker-ce/ubuntu/

# docker-compose的安装

https://docs.docker.com/compose/install/

# expect安装

expect是一个命令行的交互的工具

有些脚本需要跟命令行交互, expect就是一个选择方案

```bash
sudo apt-get install tcl tk expect
```

# 安装Tmux

Tmux 是一个运行在后台的控制台管理工具

主要用于防止网络中断 而导致ssh断开, 时工作状态的控制台缺失

特别在install 某些大服务的时候 特别好用

```bash
$ sudo apt-get install tmux
```

但是tmux有个比较不好用的地方 就是默认的鼠标滚动是不会滚动屏幕的

而是在当前命令行滚动历史的命令

所以这个要改一下

```bash
$ tmux set -g mouse on
```

