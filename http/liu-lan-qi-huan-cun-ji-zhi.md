# 浏览器缓存机制 

# 常见缓存结果

1. from disk cache 200 
2. 几百B内 304
3. from memory cache 200

# 304和200

一张图很清楚的说明何时304 何时200 (已经忘了图片来源了...)

![缓存](/assets/4125925631.png)

## 如何判断资源是否过期

这取决于

在请求资源,第一次返回来时

1. 查看response-header的cache-control、pragma: no-cahce、Expires属性
2. 查看当前html文件的cache-control、pragma: no-cahce、Expires属性

## from memory 和 from disk

这里基于chrome56做研究,不同的浏览器可能策略不一样

chrome 如果是取缓存的话

1. image去from memory的
2. js,css是from disk的
3. 小于1K的js和css也会from memory

而from memory(0ms) 肯定会比 from disk(1ms)快的

但是chrome缺不能全部存在内存中

还做了一个小实验

现在进入一个有缓存的页面,能看到上诉规律

然后关掉chrome,重新进入这个页面,会发现所有的都是from disk的

# 遗留问题

1. 如何缓存.html,如果不用manifest

# 参考链接

1. http://www.alloyteam.com/2012/03/web-cache-1-web-cache-overview/
2. 缓存设置优先级探究: http://blog.csdn.net/cor_twi/article/details/48596325
3. from disk 和 from memory: http://aircloud.10000h.top/58