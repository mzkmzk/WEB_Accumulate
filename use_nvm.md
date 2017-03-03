# nvm的使用

# 安装 

`curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash
`

# 常用命令

1. nvm ls-remote --lts: 查看远程可用的lts版本
2. nvm ls 查看本地安装的node
3. nvm exec 4.2 node 命令: 临时执行这条命令用4.2的版本
4. nvm which 4.2.6: 查看该版本node的执行路径
5. nvm use 4.2.6: 在当前窗口使用4.2.6,但是新开窗口后会返回到default

# 参考资料

1. https://github.com/creationix/nvm