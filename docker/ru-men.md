# 入门 

# 环境

mac 10.10.3

# 安装

1. 下载dmg格式安装即可https://docs.docker.com/docker-for-mac/install/
2. 切换中国源perferences->daemon->registry->mirrors添加https://registry.docker-cn.com

 检查是否加速成功了镜像
 
 ```bash
 docker info|grep "Registry Mirrors" -A 1
Registry Mirrors:
 https://registry.docker-cn.com/
 ```


# 在Dockerfile忽略指定文件

我们在Dockerfile经常执行`COPY . .`

这样把代码放进放进镜像里

但是我们可能想排除一些目录/文件

这就需要忽略指定文件

这个通过.dockerignore 来指定, 用法跟.gitignore类似

可以参考 https://qhh.me/2019/02/24/dockerignore-%E6%96%87%E4%BB%B6%E4%BB%8E%E5%85%A5%E9%97%A8%E5%88%B0%E5%AE%9E%E8%B7%B5/