# 函数节流

# 需求

在react里,做了个监听scroll,在某div快到底部时,加载数据

为了快到底部的时候就能记载数据而非完全到底部了才加载数据

scroll里我写的函数式

```javascript
if (target.scrollTop + target.clientHeight + 300 >= target.scrollHeight) {
    this.props.load_data()
}
```

原因出在这个+300,因为如果不加的话 完全等于(既到底部),他是无法再滚动的,所以也就不会继续出发scroll

但是+的话 出发了scroll,到底部还有300px的距离,还能scroll很多次呢

所以为了防止这样的情况发生,需要做

1. 我的react有is_fetching,监听是否加载中,判断如果加载中,则不处理
2. 函数节流,因为component里到action到reducers里改变is_fetching是需要时间的,而在这段时间里,scroll可能已经触发了很多次了,所以要做节流,即要setTimeout一下,给时间is_fetching属性的改变传递争取时间

# how节流

## JavaScript高级程序设计的demo

```javascript
 function throttle(method, context) {
     if(method._throttleID !== undefined)  clearTimeout(methor._throttleID);
     method._throttleID = setTimeout(function(){
         method.call(context);
     }， 100);
 }
 
//使用
window.onresize = function(){
    throttle(myFunc);
}
```

但是这样会有一个明显的问题

就是当我们要给myFunc传递event时,怎么破?那就要改throttle,多加参数了,不灵活

## impress里的

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

这个throttle是返回的函数,而且能把默认的arguments都传递给自定义的函数

# 参考链接

1. http://www.alloyteam.com/2012/11/javascript-throttle/