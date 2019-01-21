# NodeJs在多平台运行的坑坑洼洼

# window和linux 设置环境变量的不同

我们经常会在package.json里设置不同的打包命令

用来区分是打测试环境的包还是正式环境的包

```javascript
{
    "scripts": {
        "dev": "cross-env NODE_ENV=dev node webpack.config.js ",
        "production": "cross-env NODE_ENV=production node webpack.config.js "
    }
}
```

这里主要用了[cross-env](https://www.npmjs.com/package/cross-env) 

来兼容window和linux设置环境变量的不同

# spawn 执行 命令 多平台命令名 不一致

这里以 npm install为例

用child_process的spawn执行 npm install时, 

window的第一个参数要是npm.cmd 而linux则是npm

为了兼容不同平台 用了 [cross-spawn](https://www.npmjs.com/package/cross-spawn)

```javascript
let { spawn } = require('cross-spawn')

let spawnInstall = spawn.sync('npm', ['install'], {
    cwd: path.join(__dirname, '../','template', 'jquery')
})
```

# spawn 执行命令 尽量使用局部命令

像babel-cli webpack 等这些命令 和容易直接写成

```javascript
let { spawn } = require('child_process')

spawn('webpack', ...)
```

假设开发者本机上全局装了 webpack 就会OK

但是没装就不行了 

所以我们需要把webpack安装在本项目的 node_modules 并且 使用node_modules的webpack命令

在package.json 里增加 webpack

```javascript
spawn((path.join(__dirname, '..','node_modules', '.bin', 'webpack'), ...)
```

在node_modules里假设插件里有命令行设置 那么都会在本项目的node_modules/bin里

