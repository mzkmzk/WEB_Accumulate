# 常见错误 

## Error: spawn cp  ENOENT

错误代码

```javascript
events.js:161
      throw er; // Unhandled 'error' event
      ^

Error: spawn cp  ENOENT
    at exports._errnoException (util.js:1028:11)
    at Process.ChildProcess._handle.onexit (internal/child_process.js:193:32)
    at onErrorNT (internal/child_process.js:359:16)
    at _combinedTickCallback (internal/process/next_tick.js:74:11)
    at process._tickCallback (internal/process/next_tick.js:98:9)
    at Module.runMain (module.js:607:11)
    at run (bootstrap_node.js:422:7)
    at startup (bootstrap_node.js:143:9)
    at bootstrap_node.js:537:3
```

我的代码
```javascript
    child = child_process.spawn('cp ', ['-r', src_path  ,'./']);

    child.stdout.on('data', (data) => {
      console.log(`stdout: ${data}`);
    });

    child.stderr.on('data', (data) => {
      console.log(`stderr: ${data}`);
    });

    child.on('close', (code) => {
      console.log(`child process exited with code ${code}`);
    });
```

排除错误的代码

```javascript
(function() {
    var childProcess = require("child_process");
    var oldSpawn = childProcess.spawn;
    function mySpawn() {
        console.log('spawn called');
        console.log(arguments);
        var result = oldSpawn.apply(this, arguments);
        return result;
    }
    childProcess.spawn = mySpawn;
})();
```

输出

```bash
spawn called
{ '0': 'cp ',
  '1':
   [ '-r',
     '/Users/maizhikun/Learning/apache_sites/K-Cli/Template/React',
     './' ] }
```

> 原因

看到输出后...就立马能想到是因为cp后面的空格.........

> 参考链接

1. 排查错误的代码: http://stackoverflow.com/questions/27688804/how-do-i-debug-error-spawn-enoent-on-node-js


# Mac运行Node相关命令行库发生 env: node\r: No such file or directory

当node脚本是创建在window时, DOS格式`\r\n`

而unix默认的'\n'

所以mac中找不到`#!/usr/bin/env node`

## 解决方案

第一种是
```javascript
vim script

:se ff=unix
:wq
```
找到安装在全局的node执行脚本, vim进去 设置ff

亲测有效

另外两种方案, 不过还没亲自校验过

第二种是

通过更改format 其实原理一样

```bash
brew install dos2unix
sudo dos2unix /usr/local/lib/node_modules/task-app/src/task-app.js //每个人的node路径不一定一致
```

第三种 

自行修改format
```javascript
cat your_script.js | tr -d '\r' > fixed_script.js
```


> 参考链接

1. https://stackoverflow.com/questions/30344858/node-script-executable-not-working-on-mac-env-node-r-no-such-file-or-directo