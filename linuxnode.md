# Linux安装NODE

# NVM

https://github.com/creationix/nvm

选择NVM的原因是它可以方便的切换版本

## 步骤

1. curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.1/install.sh | bash, 试用前可以其github查看最新的安装url
2. 新开窗口,输入`nvm ls-remote`,查看可安装的版本
3. 安装node:  nvm install v6.10.0
4. 校验安装是否成功: node -v