# JavaScript内存泄露及分析

# 例子

## 全局作用域问题

业务代码
```javascript
let global_window = {
    init: () => {
        a = []
        for (var i = 0; i < 300; i++) {
            a[i] = new Array(1000000).join('*')
        }
    }
}

module.exports = global_window
```

测试代码
```javascript
let utils = require('../utils')

it('global_window 无内存泄露', function(done){
   
    let global_window_true = require('../global_window_true.js')
    console.log('global_window_true 启动时内存占用: ' + utils.get_memory())
    global_window_true.init()
    global.gc() //手动清理GC
    setTimeout(function(){ //加个延迟, 不然rss可能还没回收完毕
        console.log('global_window_true 结束时内存占用: ' + utils.get_memory())
        done()
    }, 1000)
    
})
```

输出结果

```
global_window_true 启动时内存占用: 进程常驻内存:  99.8 MB, 已申请的堆内存: 72.4 MB, 已使用的内存: 40.1 MB
global_window_false 结束时内存占用: 进程常驻内存:  404 MB, 已申请的堆内存: 378 MB, 已使用的内存: 335 MB
```
