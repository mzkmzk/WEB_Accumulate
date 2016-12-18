# queue

# 简介

队列,就是当一坨函数要顺序执行要用到的东西

# API

## .queue

作用: 放入队列

```javascript
.queue([queueName])
.queue([queueName],newQueue)
.queue([queueName],callback)

queueName: 默认'fx':String
newQueue: :Array
callback: :function
```

## .dequeue

作用: 执行下一个队列函数,并执行出列

```javascript
.queue([queueName])

queueName: 默认'fx':String
```

## .clearQueue

作用: 清除队列

```javascript
.clearQueue([queueName])

queueName: 默认'fx':String
```

# 结构

```javascript
jQuery.extend({
    queue( elem, type, data ) //对elem进行存储key为type,value为data,并返回最终的队列data
    dequeue( elem, type ) //出列
    _queueHooks(elem, type)//生成或返回当前执行函数的队列钩子
})
```

# 注意点

## 出列操作

## 生成钩子

```javascript
_queueHooks: function( elem, type ) {
    var key = type + "queueHooks";
    return dataPriv.get( elem, key ) || dataPriv.access( elem, key, {
        empty: jQuery.Callbacks( "once memory" ).add( function() {
            dataPriv.remove( elem, [ type + "queue", key ] );
        } )
    } );
}
```




