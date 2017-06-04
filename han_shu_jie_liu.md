# 函数节流与反抖

# 节流需求

## underscore的throttle简介

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

这种情况其实很常见, 如果没有设置上面两个参数, 并且假设`|`设置了ajax请求, 那么很容易连续触发两次ajax, 这是绝对不允许的

一般ajax的话, 建议`leading: false`

## underscore的throttle源码

```javascript
  _.throttle = function(func, wait, options) {
    var timeout, context, args, result;
    var previous = 0;
    if (!options) options = {};

    var later = function() {
      previous = options.leading === false ? 0 : _.now();
      timeout = null;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    };

    var throttled = function() {
      var now = _.now();
      if (!previous && options.leading === false) previous = now;
      var remaining = wait - (now - previous); // 代表还有多少时间, 可以执行下一次函数
      context = this;
      args = arguments;
      
      if (remaining <= 0 || remaining > wait) { //只有当没有设置options.leading = false和 非节流的情况第一次能进来这里. 
        if (timeout) { // 虽然 remaining不可能超过wait, 这里没必要clearTimeout(timeout), 因为如果setTimeout能准确执行的话, 这里timeout肯定不存在
          clearTimeout(timeout); //但是seTimeout 并不准确, 可能会延迟, 所以可能到了超过remaining的时间, 但setTimeout还没执行, 所以要移除掉
          timeout = null;
        }
        previous = now;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      } else if (!timeout && options.trailing !== false) {// 被节流的函数都要执行setimeout, 
        timeout = setTimeout(later, remaining);
      }
      return result;
    };

    throttled.cancel = function() {
      clearTimeout(timeout);
      previous = 0;
      timeout = context = args = null;
    };

    return throttled;
  };
```

## 简单做法

underscore的throttle虽然是功能强大, 但是我们有没有办法做出一个建议的节流呢

如果非通用的话 ,这里就是一个节流

```javascript
var resizeTimer=null;
$(window).on('resize',function(){
       if(resizeTimer){
           clearTimeout(resizeTimer)
       }
       resizeTimer=setTimeout(function(){
           console.log("window resize");
       },400);
   }
);
```


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
3. http://www.css88.com/archives/4648: 节流说法