# JavaScript内存泄露及分析

# JavaScript内存管理

JavaScript的语言引擎有一张"引用表"

保存着内存里面所有资源(通常是各种值)的引用次数

如果一个资源的引用次数是0, 那么该资源的内存即可释放

# 什么是内存泄露

当一个资源在开发者理解中是不可能再用了

而OS认为其还在被运用, 不对其进行垃圾回收

则为内存泄露

# 内存泄露的类型 

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

demo地址: http://demo.404mzk.com/js_mermory/closure_circulation_use/closure.html

```javascript
'use strict';

function LeakObject () {
  this.value = new Array(1024 * 1024).join('X');
}

var leakObject = null;

function addLeak() {
  var oldObj = leakObject;
  leakObject = {
    leakObj: new LeakObject(),
    closure: function () {
      console.log(oldObj);
    }
  };
}

function releaseLeak () {
  leakObject = null;
}

```
这里每执行一次addLeak 都会引发一次内存泄露

为什么, 因为他们形成了一个链表

这里假如执行了3次addLeak会有三个oldObj泄露了

第一次执行addLeak

链表如图所示

![链表](/assets/QQ20180301-131637.png)

第一次产生的'xxxx', 存在其实是正常的, 和闭包没关系,只不过因为xxx保存在window.leakObject.leakObj里而已

而看下第二次执行addLeak

![第二次addLeak内存](/assets/QQ20180301-131815.png)

注意下其链表value->leakObj->oldObj->closure->window.leakObject

注意下这里的closure

因为closure保存了oldObj, 而导致其oldObj.leakObj也无法被释放

以此类推, 以后每次泄露的oldLeak 都可以追溯的window.leakObject

所以要释放这个内存泄露 只需要window.leakObject = null即可 

## 魔鬼eval和 new Function

1. 间接使用eval会导致其作用域在全局, 而如果在非严格下使用eval, 会导致eval可以创建变量
2. new Function其函数字符串, 会在全局, 并且其作用域是全局的 

new Function的话 之前发现undescore里的 _.template 是 用new Function实现的, 所以说每次template的函数字符串

都是无法被GC的 

## 分离DOM泄露

demo体验地址: http://demo.404mzk.com/js_mermory/closure_circulation_use/dom.html

DOM已不在DOM树, 而JS变量扔保留着节点信息 

发现此类节点一般在memory->录取一个HEAP SNAPSHOTS->过滤框搜索`Detached`

```javascript
var leakObject = null
function addLeak() {

      var ul = document.createElement('ul');
      for (var i = 0; i < 10; i++) {
        var li = document.createElement('li');
        ul.appendChild(li);
      }
      leakObject = ul;

      document.body.appendChild(ul)
      document.body.removeChild(ul)
      
}

function releaseLeak () {
  leakObject = null;
}
```

执行一次addLeak之后 发现已分离DOM如下

![已分离DOM](/assets/QQ20180302-082450.png)

DOM节点有可能三种颜色

1. 白色: DOM仍处于DOM树中
2. 黄色: DOM不在DOM树, 但是JavaScript有引用其变量
3. 红色: DOM不在DOM树, javascript也没有引用其变量, 但是仍然没被回收

这类原因隐蔽是例如一个UL(黄色)被JS引用, 但是UL下面还有很多个LI是红色

所以解决方案是把黄色的UL处理掉, 下面的LI也会被回收 

> 子树导致整颗树无法被GC

可体验DEMO: http://demo.404mzk.com/js_mermory/closure_circulation_use/dom4.html

假如有颗树是这样的 tree节点->middle_div节点(保存着大量数据)->little_span节点

当tree.innerHTML = ''之后

DOM全部脱离DOM树

此时假如 little_span仍被JS引用的话, middle_div的数据会被GC吗

```javascript
var tree = document.getElementById("p") ,
    need_div = null
    
function createTree() {


  var middle_div = document.createElement("div"),
      little_span = document.createElement("span")

  
  middle_div.data = new Array(1024 * 1024).join('x');
  tree.appendChild(middle_div); 

  little_span.id = 'little_span'
  middle_div.appendChild(little_span)  

  need_div = document.getElementById('little_span') 
}

function detachTree(){
  tree.innerHTML = ''
}
function removeTree(){
  tree = null
}
function removeLeak(){
  need_div = null
}

```

因为little_span被JS引用了, 导致整个tree即使脱离了DOM都无法被GC,只有little_span = null 之后才能GC

内存泄露快照

![DOM内存泄露](/assets/QQ20180302-092531.png)

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





# 参考资料

1. 垃圾回收机制: http://www.ruanyifeng.com/blog/2017/04/memory-leak.html
2. google官网内存讲解: https://developers.google.com/web/tools/chrome-devtools/memory-problems/memory-101?hl=zh-cn
3. eval和new Function作用域: https://www.jianshu.com/p/db7ec7b51933
4. 常见内存泄露距离: https://www.jianshu.com/p/0191c6937917
5. 英文版 详解内存泄露的原因和排查方式: https://auth0.com/blog/four-types-of-leaks-in-your-javascript-code-and-how-to-get-rid-of-them/

