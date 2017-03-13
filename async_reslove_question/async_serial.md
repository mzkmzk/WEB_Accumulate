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

