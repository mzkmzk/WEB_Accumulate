# Tmux简介

# 安装tmux

```bash
$ sudo apt-get install tmux
```

# 基本命令

```bash
# 快速创建tmux窗口
$ tmux
# 创建带名字的tmux窗口
$ tmux new -s 窗口名字
# 退出tmux
$ ctrl+b d
# 关闭当前tmux窗口
$ exit
```

# 常用命令

### tmux attach

```bash
# 查看现有窗口
$ tmux ls 
docuker-compose-install: 1 windows (created Thu Mar  5 22:43:21 2020) [206x45]
generate-ca: 1 windows (created Mon Mar 23 18:01:26 2020) [157x37]
# 直接-t 窗口名即可
$ tmux attach -t generate-ca
```