# 函数节流与反抖

# 节流需求

为了指定时间内函数只被触发一次

`.`代表函数被触发时, `|`代表函数真正执行了, 这里显示每1秒触发一次函数`.`, 限定每3秒内只能触发一次

这里以underscore的throttle为例

`.|...|...|`

这种是最正常的情况

首先触发了一次`.`, 然后函数立马执行,1 然后中途多次触发`.`, 但是每秒才能触发一次

比较特殊的情况, 执行了2次`.`

按理来说, 只会触发一次函数, 但underscore实际触发了两次

`.|.|`

underscore的throttle有两个参数

1. leading: false;//这里如果设置了 会阻止第一个`|`被执行
2. trailing: false; //这里如果设置了 会阻止第二个`|`被执行



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