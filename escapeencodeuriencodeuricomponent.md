# encape、encodeURI和encodeURIComponent的总结

# 区别点

## 特殊符号是否编码

1. escape: 不编码ASCII字母、数字、标点符号"@ * _ + - . /"，对其他所有字符进行编码。
2. encodeURI: 不编码ASCII字母、数字、标点符号"; / ? : @ & = + $ , #",对其他所有字符进行编码。
3. encodeURIComponent: 对所有字符进行编码

## 转码结果

1. escape: 输出字符的Unicode编码值
2. encodeURI: 输出符号的utf-8形式，并且在每个字节前加上%
3. encodeURIComponent: 输出符号的utf-8形式，并且在每个字节前加上%

tips:

1. 但是我们知道，网页在提交表单的时候，如果有空格，则会被转化为+字符。服务器处理数据的时候，会把+号处理成空格。

# 注意点

## 为何解码最好解两次

例如笔者常用的解码方式

```javascript
function decode(str) {
    var r = '';
        try {r = decodeURIComponent(decodeURIComponent(str));   
    }catch(e){
        try {r = decodeURIComponent(str);} catch(e) {r = str;}
    }
    return r;
}
```

1. 为什么捕抓异常: 其实decodeURIComponent是会有时候会爆异常的..这个属于bug
2. 为什么要尝试两次编码、一次编码、和无编码: 因为防止服务端encode两次、一次、或者无编码

# 参考链接

1. http://www.ruanyifeng.com/blog/2010/02/url_encoding.html