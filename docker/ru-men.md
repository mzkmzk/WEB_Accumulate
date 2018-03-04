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
 
 
# Docker中跑node

1. 下载node镜像