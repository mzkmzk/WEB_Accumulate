# cookie中samesite的介绍

# 简介

在chrome80的版本(2020年2月左右的版本)中逐渐灰度对samesite从默认值为`NONE` 改为`LAX`

我们需要对samesite做一个比较全面的认识

samesite主要用来设置防止CSRF攻击的

# Chrome80中灰度的SameSite相关事情

- 对samesite从默认值为`NONE` 改为`LAX`
- 对samesite设置为`NONE`的情况下 必须同时设置`Secure`

第二点就是

这样的设置不起作用

```javascript
document.cookie="widget_session=abc123; SameSite=None"
```

需要更改为

```javascript
document.cookie="widget_session=abc123; SameSite=None; Secure"
```

这样这个cookie在同根站点的情况下, 只有https的请求才会带

# SameSite和各类请求的发送情况

### SameSite=None

这种情况在任何第三方域名 发送aaa.com域名下的请求, aaa.com相关的`SameSite=None`的Cookie会被带上

### SameSite=Lax

这种情况下任何第三方域名发送aaa.com域名下的请求,  aaa.com相关的`SameSite=Lax`的Cookie都不会被带上

chrome也有计划将post的xhr 去除带上`SameSite=Lax`的请求 

### SameSite=Strict

这个跟`SameSite=Lax`类似,

笔者现在唯一的区别只发现了

假设从第三方域名, 通过a标签跳转到`aaa.com`中的页面

aaa.com相关的`SameSite=Lax`的Cookie会被带上

而`SameSite=Strict`相关的Cookie没被带上

# 参考链接

- https://web.dev/samesite-cookies-explained/
- https://www.chromestatus.com/features/5088147346030592