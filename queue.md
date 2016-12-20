# queue

# 简介

队列,就是当一坨函数要顺序执行要用到的东西

# API

## jQuery.queue(elem, type, data)

返回或修改匹配元素关联的函数队列

## jQuery.dequeue(elem, type)

出队并执行匹配元素关联的函数队列中下一个函数

## .queue

作用: 返回第一个匹配元素关联的函数队列,或修改所有匹配元素关联的函数队列

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

## .promise(type, object)

返回一个队列的只读副本,观察每个匹配元素关联的某个类型的函数队列和计数器是否完成

# 结构

```javascript
jQuery.extend({
    queue( elem, type, data ) //对elem进行存储key为type,value为data,并返回最终的队列data
    dequeue( elem, type ) //出列
    _queueHooks(elem, type)//生成或返回当前执行函数的队列钩子
})

jQuery.fn.extend( {
    queue: function( type, data){},
    dequeue: function( type ){},
    cleaQueue: function( type ){},
    promise: function( type, obj ){}
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

可在elem添加empty属性,方便去掉队列里的缓存数据




