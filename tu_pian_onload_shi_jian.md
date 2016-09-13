# 图片Onload事件

其实这里说的图片onload事件

video和audio等多媒体也有类似的问题

# 问题

1. 它在跨浏览器下始终是不可靠
2. 如果被设置图片的src和之前的src相同，在WebKit中，该事件不能正常触发(需要先绑定onload,然后改变src)
3. 不能正常在DOM上冒泡
4. 如果图片已经换成,将停止触发

# 解决办法

## 用捕抓

```javascript
 div1 = document.getElementById("ul_index");
div1.addEventListener("load",function(ev) {
    ev = ev || window.event;
    console.log( ev.target );
},true)
```

当然弊端很明显弊端不支持IE9以下

