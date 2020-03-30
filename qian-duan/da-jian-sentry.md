# 搭建Sentry

# Install

```bash
$ git clone git@github.com:getsentry/onpremise.git
$ cd onpremise
# 安装
$ ./install.sh
# 部署
$ docker-compose up -d
```

# 安装之后要做的配置

### 配置SDK上传url前缀

在'sentry/config.yml'中添加

```bash
system.url-prefix: 'https://sentry.404mzk.com'
```

这样UI展示的引入SDK设置就会我们的url前缀

### 创建 Team与项目

在web界面的`Settings -> Teams -> Create Team`

### 创建 Project

在web界面的`Project -> Create Project`

### 设置时区和语言

`Settings->Account Details Timezone`和`Language`

# 在各技术框架引入的方式

### Vue

```javascript
import * as Sentry from '@sentry/browser'
import * as Integrations from '@sentry/integrations'

Sentry.init({
  dsn: 'https://xxxx@sentry.xxx.com/2',
  integrations: [new Integrations.Vue()]
})

```

# SourMap设置

- 设置sentry用户token

```bash
$ npm install -g @sentry/cli
$ sentry-cli --url https://sentry.xxx.com/ login
```

执行完命令就会自动在用户家目录生成认证文件`~/.sentryclirc`

内容

```
[defaults]
url=https://sentry.xxx.com/

[auth]
token=xxxx
```

然后需要在webpack中添加

```javascript
// 笔者是在nuxt应用上使用
// 这里的config可以理解为webpack的配置对象即可
config.devtool = 'source-map'
config.output.sourceMapFilename = '[name].js.map'

config.plugins.push(new SentryWebpackPlugin({
    include: './dist/_nuxt',
    ignoreFile: '.sentrycliignore',
    ignore: ['node_modules', 'webpack.config.js'],
    configFile: 'sentry.properties',
    urlPrefix: '~/pages/_nuxt',
    release: 'v1.2.6',
    debug: true
}))
```

这里注意的是 include属性和urlPrefix属性是对应关系的

在我本地中 我的js在`./dist/_nuxt`里

而url访问 我的js路径是`https://xxx/pages/_nuxt`

对应好就ok

sentry.properties主要要存放跟sentry相关的信息

```bash
defaults.url=https://sentry.xxx.com/
# 在org在settings获取 https://sentry.404mzk.com/settings/sentry/
defaults.org=sentry
defaults.project=xxx-vip-web
# token在sentry服务器中获取 https://sentry.xxx.com/settings/account/api/auth-tokens/
auth.token=xxxx
```

这里的release注意要和`Sentry.init()`时的release报错一致

然后执行webpack打包则会上传sourmap和打包后的JS

观察sentry服务器的`releases`-> 选中tag->`artifacts`中查看上传到服务器的文件

# 设置sentry服务器配置相关信息

方式有三种

文档说明: https://docs.sentry.io/cli/configuration/

这里笔者还行比较喜欢用`@sentry/webpack-plugin中的configFile`这种方式

### .sentryclirc

这个可以设在项目中的`.sentryclirc`也可以设置在`~/.sentryclirc`

内容形式大致为

```
[defaults]
url=https://sentry.xxx.com/
org=sentry
project=xxx-vip-web

[auth]
token=xxx
```

# @sentry/webpack-plugin中的configFile

使用`@sentry/webpack-plugin`来上传文件的话, 有个选项`configFile`

一般命名为`sentry.properties`, 内容大致为

```

defaults.url=https://sentry.xxx.com/
defaults.org=sentry
defaults.project=xxx-vip-web

auth.token=xxx
```

# export环境变量 

```bash
export SENTRY_URL=https://sentry.xxx.com/
export SENTRY_ORG=sentry
export SENTRY_PROJECT=xxx-vip-web
export SENTRY_AUTH_TOKEN=xxx
```


# 遇到问题

### nginx上传文件大小限制

如果项目比较大的话 可能会触达到nginx文件上传大小限制

然而sentry可能只会告诉你`error: API request faile`

在nginx里改下上传大小就好了

主要是更改`client_max_body_size`

```bash
server {  
    server_name sentry.xxx.com;  
    location / {
        client_max_body_size    100m;
        proxy_pass http://xxxx:9000/;
    }
}
```

### could not determine a source map reference (未解决)

警告

```
~/pages/_nuxt/029be8d0acd3e6cddd1d.js (sourcemap at pages/2020/video/pc/index.js.map)
      - warning: could not determine a source map reference (Could not auto-detect referenced sourcemap for ~/pages/_nuxt/029be8d0acd3e6cddd1d.js.)
```

这里在上传sourmap时报的 但不知道具体的原因