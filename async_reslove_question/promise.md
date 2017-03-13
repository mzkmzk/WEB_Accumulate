# promise

# 最佳实践

* 总是在 .then() 里面使用 return 来返回 promise 对象或者同步值
* 总是在 .then() 里面 throw 同步的 Error 对象
* 总是使用 .catch() 来捕获错误