# JavaScript内存泄露及分析

# JavaScript内存管理

JavaScript的语言引擎有一张"引用表"

保存着内存里面所有资源(通常是各种值)的引用次数

如果一个资源的引用次数是0, 那么该资源的内存即可释放

# 什么是内存泄露

当一个资源在开发者理解中是不可能再用了

而OS认为其还在被运用, 不对其进行垃圾回收

则为内存泄露

# 发现内存泄露

我们如何发现内存泄露

# 应用工具

在查找内存泄露时, 不可避免的就是用工具去查找内存泄露的点, 下面就介绍常用的工具

## chrome

> memory

chrome比较精华的内存分析工具就在于memory, 是发现内存泄露的利器

在运用它之前, 先了解一下它的用途

![memory内存图](/assets/QQ20180219-221825.png)

1. Distance: 代表该对象距离跟window的距离
2. Object Count: 表示该项Constructor占有了对象数和比例
3. Shallow Size: 浅层大小, 指对象自身占用内存的大小
4. Retained Size: 这是将对象本身连同其无法从 GC 根到达的相关对象一起删除后释放的内存大小

Shallow Size还有需要注意的地方

1. 通常只有数组和字符串会有明显的浅层大小
2. 不过字符串和外部数组的主存储一般位于渲染器内存中, 仅将一个小包装前对象置于javascript堆上
3. 渲染器内存是渲染检查页面的进程的内存总和: 原生内 + 页面的JS堆内存 + 页面启动的所有专用工作线程的JS堆内存, 单位字节
4. 尽管如此, 即使一个小对象也可能阻止其他对象被自动垃圾回收进程处理的方式间接地占用大量内存, 单位字节

Retained Size还有需要注意的地方

GC根有很多, 但是大部分都不需要用户关注, 从应用角度来看, 存在以下种类的根

1. Window全局对象(位于每个iframe中)
2. 文档DOM树
3. 有时程序被调试当中的上下文和DevTools控制保留, 在调试程序中清除控制台并移除活动断点, 创建堆快照

memory不同的快照类型

![memory不同的快照类型](/assets/QQ20180220-195409.png)

1. Summary: 按构造函数名称分组显示, 此视图可以根据构造函数的分组类型深入了解对象的内存使用情况, 此视图特别适合查找DOM泄露
2. Comparison: 显示两个快照之间的不同, 此视图可以比较两个或多个内存快照的差异, 检查某个操作前后的差异、检查已释放内存的变化额参考计数来确认内存泄露及其原因
3. Containment: 从window对象的对象结构视图, 此视图可以分析闭包以及在较低级别深入了解应用的对象
4. Dominators: 可以显示支配树, 对于查找聚焦点非常有用, 此视图query对象的意外引用已消失, 以及删除/垃圾回收正在运动(笔者的浏览器无此视图, 先放着, 后面有研究再补充)

注：并不是所有属性都存储在 JavaScript 堆上。不会捕捉使用执行原生代码的 getter 实现的属性。另外，也不会捕捉数字等非字符串值



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

闭包本身并非非常危险, 但是闭包+setInterval 就会经常掉坑里 

```javascript
var theThing = null;
var run_num = 10;
var replaceThing = function () {
  if (run_num-- == 0){
    //clearInterval(interval_id) //'1'
  }

  var originalThing = theThing;
  var unused = function () {
    if (originalThing) { } // '2'
     
  };
  theThing = {
    longStr: new Array(1000000).join('*'),
    someMethod: function () {
      
    }
  };
  //originalThing = null //'3'
};

var interval_id = setInterval(replaceThing, 1000);
```

以上代码会引起内存泄露的点在于

1. replaceThing每执行一次, 因为代码中有引用外部的theThing变量, 函数都会处于活跃状态, 直至clearInterval
2. replaceThing每执行一次, theThing都会获取多一个对象
3.  

以下是 interval执行了5次的前后内存对比

![闭包内存泄露](/assets/QQ20180226-173559.png)

setInterval和普通的例如for循环机制不太一样

1. setInterval 执行函数一次后, 该函数仍然处于活跃状态
2. for循环 执行函数后, 整个函数就就可以被进行回收



# 参考资料

1. 垃圾回收机制: http://www.ruanyifeng.com/blog/2017/04/memory-leak.html
2. google官网内存讲解: https://developers.google.com/web/tools/chrome-devtools/memory-problems/memory-101?hl=zh-cn


