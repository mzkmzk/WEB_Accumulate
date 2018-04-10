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



