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
    dequeue: function( type ){},//each到jquery.dequeue中
    cleaQueue: function( type ){},
    promise: function( type, obj ){}
})
```

# 注意点

## 出列操作

## 设置去除缓存函数或获取缓存

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

## 入队

```javascript
jQuery.extend( {
	queue: function( elem, type, data ) {
		var queue;

		if ( elem ) {
			type = ( type || "fx" ) + "queue";
			queue = dataPriv.get( elem, type );

			// Speed up dequeue by getting out quickly if this is just a lookup
			if ( data ) {
				if ( !queue || jQuery.isArray( data ) ) {
					queue = dataPriv.access( elem, type, jQuery.makeArray( data ) );
				} else {
					queue.push( data );
				}
			}
			return queue || [];
		}	
	}
})
jQuery.fn.extend( {
	queue: function( type, data ) {
		var setter = 2;

		if ( typeof type !== "string" ) { //当只有一个参数data的情况下
			data = type;
			type = "fx";
			setter--;
		}

		if ( arguments.length < setter ) { //没有参数的时候  || 只有一个参数而且为string时
			return jQuery.queue( this[ 0 ], type );
		}

		return data === undefined ? //设置入队
			this :  //不知道什么傻逼情况会跳到这里
			this.each( function() {
				var queue = jQuery.queue( this, type, data );

				// Ensure a hooks for this queue
				jQuery._queueHooks( this, type ); //给元素添加empty方法

				if ( type === "fx" && queue[ 0 ] !== "inprogress" ) {
					jQuery.dequeue( this, type );
				}
			} );
	},
```

# 出列

```javascript

jQuery.extend( {
	dequeue: function( elem, type ) {
		type = type || "fx";

		var queue = jQuery.queue( elem, type ),
			startLength = queue.length,
			fn = queue.shift(),
			hooks = jQuery._queueHooks( elem, type ), //获取remove函数
			next = function() {
				jQuery.dequeue( elem, type );
			};

		// If the fx queue is dequeued, always remove the progress sentinel
		if ( fn === "inprogress" ) {
			fn = queue.shift();
			startLength--;
		}

		if ( fn ) {

			// Add a progress sentinel to prevent the fx queue from being
			// automatically dequeued
			if ( type === "fx" ) {
				queue.unshift( "inprogress" );
			}

			// Clear up the last queue stop function
			delete hooks.stop;
			fn.call( elem, next, hooks );
		}

		if ( !startLength && hooks ) {
			hooks.empty.fire();
		}
	},
})


jQuery.fn.extend( {
	dequeue: function( type ) {
		return this.each( function() {
			jQuery.dequeue( this, type );
		} );
	},
})
```



