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

所以

静态资源错误只能交给










