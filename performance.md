# performance

这是一个用来研究网络加载时间的api

IE9+才支持,http://caniuse.com/#feat=nav-timing

开始之前,建议先随便打开个网页,在console稍微体验一下

1. 了解大概有的属性: performance
2. 网页加载的时间: performance.timing
3. 每个请求所耗费的事件: performance.getEntries()



# tips

使用的时候需要注意的点

1. 资源如果是第三方加载的话,若没加上`Timing-Allow-Origin: *`头们只能获取持续时间(但是笔者亲测好像没这个问题)


# 参考链接

1. http://www.alloyteam.com/2015/09/explore-performance/
2. http://www.html-js.com/article/Brief-introduction-of-JavaScript-Resource-Timing-API-to-learn-something-every-day
