# Error事件

# 分类

1. js报错
2. 静态资源和接口



# 捕抓前的错误

## css

例如css一般放在head中,

而JS在后面才加载,就是css错误时,JS还没加载,这样我们就无法上报

解决办法只有

在加载CSS之前先写简单的js

```javascript
window.addEventListener('error', function(e) {
   window._mark_error = [];
   window._mark_error.push(e);        
}, true)
```

然后等JS加载后了来读取`window._mark_error`

# 触发机制

静态资源的404,依然不会冒泡,只能进行捕抓,而且能触发到window

![error](/assets/QQ20170218-1.png)


# onerror 和 window.addEventListener('error',function)

注意点有

onerror 不会监听静态资源404等错误,只会捕抓JS错误



而因为addEventListener在JS报错信息包含的信息

![JS报错](/assets/QQ20170218-2.png)


和onerror拥有的,其也都拥有,不过是换了个名字

所以

静态资源和接口只能交给addEventListener

JS错误可以用onerror也可以用addEventListener

# 小心死循环

在监听error的函数中,一定要注意,,如果监听error函数中本身也会有报错,那么就会死循环,最好用try catch

# 有什么错误我们难以捕抓








