# queue

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


