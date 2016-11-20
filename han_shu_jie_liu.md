# 函数节流与反抖

# 节流需求

在react里,做了个监听scroll,在某div快到底部时,加载数据

为了快到底部的时候就能记载数据而非完全到底部了才加载数据

scroll里我写的函数是

```javascript
if (check() === false && target.scrollTop + target.clientHeight + 300 >= target.scrollHeight) {
    this.props.load_data()
}
```

假设我这里check需要经过很多数据访问才能得到结果,所以我就不想scroll经常执行,那怎么办

# 做法

1. 只做限定时间内不发生两次的操作

## 只做限定时间内不发生两次的操作


```javascript
var throttle = function(fn, delay){
 	var timer = null;
 	return function(){
 		var context = this, args = arguments;
 		clearTimeout(timer);
 		timer = setTimeout(function(){
 			fn.apply(context, args);
 		}, delay);
 	};
 };
 
 window.onresize = throttle(myFunc, 100);

```


思路就是

1. 先设置一个定时器100ms内,没有同一方法执行的话,就执行这一方法
2. 假设100ms内有同一方法执行,那么久把上一个定时器清除掉(上一个放在定时器里的方法永远不会执行),然后继续等待100ms

javascript高级程序设计的实现



```javascript
 function throttle(method, context) {
     method.tId && clearTimeout(methor.tId);
     method.tId = setTimeout(function(){
         method.call(context);
     }， 100);
 }
 
//使用
window.onresize = function(){
    throttle(myFunc);
}
```

这种方法其实不好,因为如果想传递参数给myFunc是很困难的.


# how节流

## JavaScript高级程序设计的demo



但是这样会有一个明显的问题

就是当我们要给myFunc传递event时,怎么破?那就要改throttle,多加参数了,不灵活

## impress里的



这个throttle是返回的函数,而且能把默认的arguments都传递给自定义的函数

# 参考链接

1. http://www.alloyteam.com/2012/11/javascript-throttle/
2. underscore的做法: http://www.qdfuns.com/notes/17398/93ec1a3c4cf2a1ecd5f8ff1a905f2d03