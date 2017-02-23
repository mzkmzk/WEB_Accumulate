# 创建和控制外部流程

# child_process模块

```javascript
var child_process = require('child_require'),
    exec = child_process.exec;
    //回调是,错误的实例, 命令的输出, 命令输出的错误信息
    exec('ls',function(err, stdout, stderr){})
```
