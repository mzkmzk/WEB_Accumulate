# promise

# 最佳实践

* 总是在 .then() 里面使用 return 来返回 promise 对象或者同步值
* 总是在 .then() 里面 throw 同步的 Error 对象
* 总是使用 .catch() 来捕获错误

# 常见问题

## 异步并行

```javascript
const promises = [1000, 2000, 3000].map(function(timeout) {
  return later(timeout);
});
Promise.all(promises)
  .then(function(data) {
    // data = ['later_1000', 'later_2000', 'later_3000']
  })
  .catch(function(err) {
  });
```

## 异步串行

```javascript
[1000, 2000, 3000]
  .reduce(function(promise, timeout) {
    return promise.then(function() {
      return later(timeout);
    });
  }, Promise.resolve())
  .then(function(data) {
    // data = later_3000
  })
  .catch(function(err) {
  });
```

##  promise 对象间的依赖

```javascript
  Promise.resolve()
  .then(function(dataA) {
    return later(2000);
  })
  .then(function(dataB) {
    // 我们同时需要 dataA 和 dataB
  });
```

转换写法

```javascript
Promise.resolve()
  .then(function(dataA) {
    return later(2000).then(function(dataB) {
      return dataA + ':' + dataB;
    });
  })
  .then(function(data) {
    // data = later_1000:later_2000
  });
```

# 参考资料

1. promise比较易懂的讲解: http://kohpoll.github.io/blog/2016/05/02/the-promise-you-may-not-know/
