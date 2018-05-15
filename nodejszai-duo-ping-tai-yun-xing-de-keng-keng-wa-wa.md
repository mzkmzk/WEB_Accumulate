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

# spawn 执行 npm install

用child_process的spawn执行 npm install时, 

window的第一个参数要是npm.cmd 而linux则是cmd

为了兼容不同平台 用了 [cross-spawn](https://www.npmjs.com/package/cross-spawn)

```javascript
let { spawn } = require('cross-spawn')

let spawnInstall = spawn.sync('npm', ['install'], {
    cwd: path.join(__dirname, '../','template', 'jquery')
})
```