# Linux给所有用户安装node

# 背景 

在A机器中安装了一个gitlab-runner程序, gitlab-runner程序会新建一个gitlab-runner用户

而我的gitlab-runner程序 主要用来跑node打包流程

我的解决方案有两个

 - 登录gitlab-runner用户, 在当前这个用户安装node
 - 给这个机器的所有用户都安装可使用的node

这里我采用的第二种解决方案 

# 步骤

> 创建nvm所需目录

```bash
# 用来存放nvm安装不同版本的node程序
$ mkdir /usr/local/nvm
# 用来存放nvm本身的代码 
$ mkdir /opt/nvm
```

> 下载nvm代码

```bash
$ git clone https://github.com/nvm-sh/nvm.git /opt/nvm
```

> nvm下载全局默认node

```bash
$ nvm install 10.18.1
```

> 新建每个用户登录脚本

```bash
$ vim /etc/profile.d/nvm.sh
```

内容如下

```bash
export NVM_DIR=/usr/local/nvm # 导出NVM_DIR 环境变量，让nvm 安装node到该目录
source /opt/nvm/nvm.sh # 执行nvm 的命令, 激活nvm 到系统shell 中
nvm use 10.18.1 # 用户进来使用的node版本
```

# 未解决问题

> gitlab-runner登录后 nvm.sh报错

因为在profile.d/nvm.sh设置了每次登陆都激活nvm`source /opt/nvm/nvm.sh`

但是su gitlab-runner 之后会报错如何

```bash
Error: EACCES: permission denied, scandir '/root'
TypeError: Cannot read property 'get' of undefined
    at errorMessage (/usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/lib/utils/error-message.js:38:39)
    at errorHandler (/usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/lib/utils/error-handler.js:201:13)
    at /usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/bin/npm-cli.js:78:20
    at cb (/usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/lib/npm.js:225:22)
    at /usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/lib/npm.js:263:24
    at /usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/lib/config/core.js:81:7
    at Array.forEach (<anonymous>)
    at /usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/lib/config/core.js:80:13
    at f (/usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/node_modules/once/once.js:25:25)
    at /usr/local/nvm/versions/node/v10.18.1/lib/node_modules/npm/lib/config/core.js:110:20
...
```

看上去是权限问题, 但是跑CI的时候没出现这个问题, 只有手动去机器登录gitlab-runner会出现这个问题, 所以先忽略, 这个机器的其他用户没这个问题 

# 参考链接

- 在linux上为所用用户安装 NVM: https://segmentfault.com/a/1190000019929765