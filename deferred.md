# Deferred

# API

### deferred.done

当deferred.resolve被触发的时候,就会通知注册在done里面的事件触发

`deferred.done( doneCallbacks [, doneCallbacks ] )`

## deferred.fail

当deferred.reject被触发的时候,就会通知注册在done里面的事件触发

`deferred.fail( failCallbacks [, failCallbacks ] )`

## deferred.progress

添加消息回调函数

`deferred.progress( progressCallbacks [, progressCallbacks ] )`

## deferred.then

同时添加到成功回调,失败回调,消息回调函数

deferred.then( doneFilter [, failFilter ] [, progressFilter ] )

## deferred.always

添加回调函数,当异步队列处于成功或失败状态时被调用

deferred.always( alwaysCallbacks [, alwaysCallbacks ] )

## deferred.pipe

`不建议使用`

提供链式使用或过滤一下函数

`deferred.pipe( [doneFilter ] [, failFilter ] [, progressFilter ] )`

过滤的demo

```javascript
var defer = $.Deferred(),
  filtered = defer.pipe(function( value ) {
    return value * 2;
  });
 
defer.resolve( 5 );
filtered.done(function( value ) {
  alert( "Value is ( 2*5 = ) 10: " + value );
});
```

## deferred.resolve

使用指定的参数调用所有成功回调函数,异步队列进入成功状态

`deferred.resolve( [args ] )`

类似的`deferred.resolveWith( context [, args ] )`

## deferred.reject

使用指定的参数调用所有失败回调函数,异步队列进入失败状态

`deferred.reject( [args] )`

类似的`deferred.rejectWith( context [, args ] )`

## deferred.notify

使用指定的参数调用消息回调函数

`deferred.notify ( [args] )`

类似的`deferred.notifyWith( context, [args] )`

## deferred.state

判断异步队列当前的状态

`deferred.state()`





