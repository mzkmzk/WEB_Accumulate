# 第一波XSS探针实现

# 1. 发现XSS

说起来真是惭愧.

很多人如果用百度的开源富文本插件UEditor如果不在服务端过滤XSS的话.很容易引起XSS


![第一波XSS](assets/QQ20160722-0.png)

# 2. 漏洞利用

这里需要运用了<https://github.com/evilcos/xssprobe>里的代码

置入js,然后远程就可以获取到cookie和浏览器的一些信息

![xss结果](assets/QQ20160722-1.png)

