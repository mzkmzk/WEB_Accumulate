# 创建和控制外部流程

# child_process模块

```javascript
var child_process = require('child_process'),
    options ={},
    exec = child_process.exec;
    //回调是,错误的实例, 命令的输出, 命令输出的错误信息
    //options 可以的配置信息可参考 https://nodejs.org/api/child_process.html#child_process_child_process_exec_command_options_callback
    exec('ls', options, function(err, stdout, stderr){})
```
