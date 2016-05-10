# getComputedStyle

## 1. 简介

作用: 获取指定dom的所有css属性

语法: `var style = window.getComputedStyle("元素", "伪类"|null);`

eg: 

```javascript
var h3 = document.getElementById("1234"),
  style = window.getComputedStyle(h3,null)
```

output:

![getComputedStyle](QQ20160510-0.png)

## 参考链接

<http://www.zhangxinxu.com/wordpress/2012/05/getcomputedstyle-js-getpropertyvalue-currentstyle/>