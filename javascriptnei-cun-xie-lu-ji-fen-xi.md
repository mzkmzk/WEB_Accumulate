# JavaScript内存泄露及分析

# 例子

## 意外的全局变量

有内存泄露的业务代码
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

无内存泄露的业务代码
```javascript
let global_window = {
    init: () => {
        let a = []
        for (var i = 0; i < 300; i++) {
            a[i] = new Array(1000000).join('*')
        }
    }
}

module.exports = global_window
```

我们通过调试chrome看下内存有无泄露

![chrome](/assets/QQ20180212-183104.png)

三次内存的收集时机分别是

1. 加载第一行代码时
2. 执行完global_window.init后
3. 点击手动GC后

发现GC并不能收集a变量, 因为其被绑定到了global全局变量里 

除了每次在chrome进行调试, 我们还可以通过node来帮助我们完成这个功能

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
> node  --expose-gc node_modules/jest/bin/jest.js //这里的--expose-gc是暴露global.gc()给代码手动清理缓存 
global_window_false 启动时内存占用: 进程常驻内存:  100 MB, 已申请的堆内存: 75.0 MB, 已使用的内存: 35.2 MB
global_window_false 结束时内存占用: 进程常驻内存:  404 MB, 已申请的堆内存: 378 MB, 已使用的内存: 335 MB

global_window_true 启动时内存占用: 进程常驻内存:  99.8 MB, 已申请的堆内存: 72.4 MB, 已使用的内存: 40.1 MB
global_window_true 结束时内存占用: 进程常驻内存:  100 MB, 已申请的堆内存: 75.0 MB, 已使用的内存: 35.0 MB
```

可见有内存泄露代码中的a变量用了全局变量, 而导致gc后仍然占用内存 

## 闭包


