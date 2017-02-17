# mousewheel

监听滚动事件

# 一般写法

```javascript
 $(选择器).on("mousewheel DOMMouseScroll",function(event){
 }
```
mousewheel为通用的滚轮事件

而DOMMouseScroll是Fixfox的滚轮方式

## 滚轮方向

```javascript
  $(document).on("mousewheel DOMMouseScroll",function(event){
   var sd=event.originalEvent.wheelDelta||event.originalEvent.detail*-1;
  });
```

