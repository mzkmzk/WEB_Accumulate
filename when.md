# when

# API

提供了一个基于一个或多个对象的状态来执行回调函数的功能,通常是基于具有异步事件的异步队列

`jQuery.when( deferreds )`

example

```javascript
$.when(
   $.ajax(),
   $.ajax(),
   $.ajax(),
).done(whenDone).fail(whenFail)

//whenDone里得到的数组是ajax里全部success时的得到的参数
```