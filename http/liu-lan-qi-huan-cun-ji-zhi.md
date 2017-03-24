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

1. 查看response-header的cache-control、pragma: no-cahce、Expires属性
1. 有无`<META HTTP-EQUIV="Pragma" CONTENT="no-cache">`,有则完全不走缓存,但是笔者在chrome 56上尝试了,浏览器还是会读缓存
2. 


# 遗留问题

1. 如何缓存.html,如果不用manifest

# 参考链接

1. http://www.alloyteam.com/2012/03/web-cache-1-web-cache-overview/
2. 缓存设置优先级探究: http://blog.csdn.net/cor_twi/article/details/48596325
