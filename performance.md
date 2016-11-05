# performance

这是一个用来研究网络加载时间的api

IE9+才支持,http://caniuse.com/#feat=nav-timing

开始之前,建议先随便打开个网页,在console稍微体验一下

1. 了解大概有的属性: performance
2. 网页加载的时间: performance.timing
3. 每个请求所耗费的事件: performance.getEntries()

timing和getEntries有很多一样的属性,但是还是有些不一样的

# timing专属属性

1. navigationStart: 前一个页面(不一定同域)unload的时间戳,若无前一页,则与fetchStart值相等
2. unloadEventStart: 前一个页面(同域)unload回调开始的时间戳,若无前一页,则值为0
3. unloadEventEnd: 前一个页面(同域)unload回调结束的时间戳,若无前一页,则值为0
4. domLoading: 
domInteractive
domContentLoadedEventStart
domContentLoadedEventEnd
domComplete
loadEventStart
loadEventEnd



网上被复用了很多次的图

![时间加载](072455NuJ.png)


1. 



# tips

使用的时候需要注意的点

1. 资源如果是第三方加载的话,若没加上`Timing-Allow-Origin: *`头们只能获取持续时间(但是笔者亲测好像没这个问题)


# 参考链接

1. http://www.alloyteam.com/2015/09/explore-performance/
2. http://www.html-js.com/article/Brief-introduction-of-JavaScript-Resource-Timing-API-to-learn-something-every-day
