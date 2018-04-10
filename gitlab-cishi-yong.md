# GitLab-CI使用

# Specific Runners 的搭建

### runner机器的 安装

首先建议找个linux机器 或者window mac 都可以作为runner的机器

官方的安装文档 https://docs.gitlab.com/runner/install/

官方的安装源实在太太太太太慢了

所以改为清华的镜像安装(linux)

https://mirrors.tuna.tsinghua.edu.cn/help/gitlab-ci-multi-runner/

笔者的runner机器是centOS6

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
sudo yum makecache
sudo yum install gitlab-ci-multi-runner
```

