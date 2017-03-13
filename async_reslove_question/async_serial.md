# 异步串行

# 问题需求 

执行命令行

1. gitbook build
2. git add . && git commit -m "k-trainer" && git push origin master 

利用nodejs的exec

exec是个异步任务

但是现在需要串行执行

# exec的代码

```javascript
 var child = exec(element.command, element.options,  (error, stdout, stderr) => {
             
});
```

# queue

这个是最终的解决方案,所以放在前面

利用`https://www.npmjs.com/package/queue`

queue,实现极其简单,充分利用nodejs的event模块

```javascript
//queue,默认是N多个任务可并行的,如果希望串行,则设置concurrency为1
var q = queue({
    concurrency: 1
});
[shell1,shell2].forEach(function(element){
    q.push(function(next){
        exec(element.command, element.options, (error, stdout, stderr) => {
        next();//通知执行下一个任务
        });
    }
})
```

# promise

```javascript
var promise = Promise.resolve()

[shell1,shell2].forEach(function(element){
    promise = promise.then(function(){
        return new Promise(function(resolve, reject){  
            exec(element.command, element.options, (error, stdout, stderr) => {
                resolve();//通知执行下一个任务
            });
        });
    })
})

```

