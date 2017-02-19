# onload事件

# 背景

在做前端监控时,想通过performance做一个资源超时报警

但是我们无法知道资源何时加载完毕(可能用户点击一下,就会去请求一个img)

# load的冒泡和捕抓

## img请求

img的请求是不会冒泡的,但是会经过捕抓.查看捕抓的path

![img冒泡](/assets/QQ20170218-0.png)

并不会经过window,而是从document开始

所以,可以在document.html.body及其其他祖先元素和本身触发load事件

## video请求

video 是没有load事件的

## 背景图片

背景图片不触发load事件

## window的load

只有当页面上所有的图像,css,js加载完毕时触发,

