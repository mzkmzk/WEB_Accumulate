# 深剖定时器

# 定时器类型

1. setTimeout: 指定xx时间后执行回调
2. setInterval: 指定每隔xx时间后定期执行
3. setImmediate: 和setTimeout(fn, 0)类似, 执行setTimeout(fn, 0)和setImmediate的顺序问题, 不同环境有差异 (chrome不支持、 IE/node支持)
4. requestAnimationFrame: 跟着屏幕刷新而执行回调, 一般60HZ对应的间隔是16.7ms, 一般用作处理动画

# 触发时机

要正确的认识定时器的触发时机

需要先了解下macrotasks和microtasks

这两类任务都是异步的任务

那我们这些异步任务是怎么分类的呢

macrotasks: setTimeout, setInterval, setImmediate, requestAnamationFrame, I/O, UI渲染

microtasks: Promise, process.nextTick, Object.observe, MutationObserver

# 思考题

### 防止定时器在少于指定的间隔时间内执行

例如有个函数a, 需要相隔1000ms 执行, 所以写了

```javascript
setInterval(function(){
    a()
}, 1000)
```

但是问题在于, a函数执行10ms, 所以下次再执行a 会是 1000 - 10 ms之后

这样个别情况就会有问题

所以需要更为严格的不能少于指定时间内执行的话..可以类似这么写自行利用setTimeout去实现setInterval 就可以规避掉这个问题

具体例子可见, (http://demo.404mzk.com/setinterval/) 中的更严格时间间隔

setTimeout实现setInterval 的方法

```javascript
var my_setinterval = function(func, timeout, end_callback) {
      setTimeout(function() {
        var result = func();

        if ( result === false) {
            end_callback && end_callback()
            return
        } 

        my_setinterval(func, timeout);
      }, timeout);
    },
```

# 小tips

### 回调函数中的参数(不支持IE8)

```javascript
var test_bibao = function(){
    
    for (var i = 0; i < 10; i++) {
        setTimeout(function(){
            document.querySelector('.j_test_params_1').innerHTML +=  i + ' '
        },0)
        
        setTimeout((function(a){
            document.querySelector('.j_test_params_1').innerHTML +=  a + ' '
        })(i) , 0)


        setTimeout(function(a){
            document.querySelector('.j_test_params_2').innerHTML +=  a + ' '
        },0, i)
    }
}
test_bibao()
```

这算是比较经典的闭包引起的问题了

第一个setTimeout中 因为for循环之后才执行的输出, 所以i一直都是10

虽然解决这个问题可以通过第一个settimeout这种方式

而最好的方式还是第三种, delay参数之后的参数 都会被放进回调的参数列表中

### 回调中的this

简单的说下 setTimeout中的this是全局环境 浏览器中就是window

### setTimeout(function(){}, 0)的作用

作用主要有两个

1. 调节事件执行的顺序
2. 防止阻塞

调节顺序的例子有事件的冒泡机制

例如

1. j_1、j_2元素都监听了click
2. j_2是j_1的子元素
3. 点击j_2时, 需要先执行j_1中的监听回调, 再执行j_2的监听回调

```javascript
var j_1 = document.getElementById('j_1'),
    j_2 = document.getElementById('j_2')

j_2.onclick = function A() {
  setTimeout(function() {
    ...
  }, 0)
};

j_1.onclick = function C() {
  ...
};
```

防止阻塞的例子有, 当需要不断改变背景色时

```javascript
var div = document.getElementsByTagName('div')[0];

// 写法一
for (var i = 0xA00000; i < 0xFFFFFF; i++) {
  div.style.backgroundColor = '#' + i.toString(16);
}

// 写法二
var timer;
var i=0x100000;

function func() {
  timer = setTimeout(func, 0);
  div.style.backgroundColor = '#' + i.toString(16);
  if (i++ == 0xFFFFFF) clearTimeout(timer);
}

timer = setTimeout(func, 0);
```




# 参考链接

1. 阮一峰的基础讲解文章: http://javascript.ruanyifeng.com/advanced/timer.html#toc1
