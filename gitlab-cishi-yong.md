# GitLab-CI使用

# Specific Runners 的搭建

### runner机器的依赖安装

首先建议找个linux机器 或者window mac 都可以作为runner的机器

官方的安装文档 https://docs.gitlab.com/runner/install/

官方的安装源实在太太太太太慢了

所以改为清华的镜像安装(linux)

https://mirrors.tuna.tsinghua.edu.cn/help/gitlab-ci-multi-runner/

笔者的runner机器是centOS6

tips 查询本机CPU版本和centos版本的命令为

```shell
> uname -a
Linux a.com 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
> lsb_release -a
LSB Version:	:base-4.0-amd64:base-4.0-noarch:core-4.0-amd64:core-4.0-noarch:graphics-4.0-amd64:graphics-4.0-noarch:printing-4.0-amd64:printing-4.0-noarch
```

```bash
> /etc/yum.repos.d/gitlab-ci-multi-runner.repo
```

vim 进去别写入内容

```bash
[gitlab-ci-multi-runner]
name=gitlab-ci-multi-runner
baseurl=https://mirrors.tuna.tsinghua.edu.cn/gitlab-ci-multi-runner/yum/el6
repo_gpgcheck=0
gpgcheck=0
enabled=1
gpgkey=https://packages.gitlab.com/gpg.key
```

然后进行yum 安装

```bash
sudo yum makecache //是 将服务器上的软件包信息 现在本地缓存,以提高 搜索 安装软件的速度
sudo yum install gitlab-ci-multi-runner
```

### 新建一个runner和git项目相关联

先去gitlab中指定项目的Setting->pipelines中查找信息会有说明

1. gitlab ci的地址
2. 本项目的token

```bash
Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
Please enter the gitlab-ci token for this runner: 项目特定的token
Please enter the gitlab-ci description for this runner: 自定义runner描述
Please enter the gitlab-ci tags for this runner (comma separated): 10.10.1.1-master 给runner定标签 可以用'-'分隔
Whether to run untagged builds [true/false]: 默认false 是否运行未标记的版本
Whether to lock Runner to current project [true/false]: 默认fanlse 是否锁定Runner 给本项目
Please enter the executor: docker-ssh, parallels, virtualbox, 
    docker+machine, kubernetes, docker, shell, ssh, docker-ssh+machine: 输入可运行的脚本 一般为shell
```

上面的这些设置可以在gitlab中点击编辑runner来改变

如果新建顺利的话 就会在项目的Setting-pipeline中Runners activated for this project中发现你刚的runner了






