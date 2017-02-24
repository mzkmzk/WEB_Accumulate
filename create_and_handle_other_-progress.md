# 创建和控制外部流程

# child_process.exec

```javascript
var child_process = require('child_process'),
    options ={},
    exec = child_process.exec;
    //回调是,错误的实例, 命令的输出, 命令输出的错误信息
    exec('ls', options, function(err, stdout, stderr){})
```
options 可以的配置信息可参考: https://nodejs.org/api/child_process.html#child_process_child_process_exec_command_options_callback

大致可配置的有执行命令的环境和条件

例如: gid, uid, env,cwd,encoding,killSingnal等

# 优点

1. 能比较简单的拼接命令

## 缺点

1. 除了命令行参数和环境变量之外,exec()函数不允许与子进程通信
2. 子进程的输出是缓存的,结果是无法对其进行流操作,它可能会耗尽内存

# child_process.spwn 创建子进程

