# Deferred

# API

## deferred.done

当deferred.resolve被触发的时候,就会通知注册在done里面的事件触发

`deferred.done(doneCallbacks)`

## deferred.fail

当deferred.reject被触发的时候,就会通知注册在done里面的事件触发

`deferred.fail(doneCallbacks)`

## deferred.progress

`deferred.progress( progressCallbacks [, progressCallbacks ] )`

添加消息回调函数

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
