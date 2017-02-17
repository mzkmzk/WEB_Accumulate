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
  //当sd为负时 为向下滚,为正时表示向上滚
```

Fixfox采取的是后者

`event.originalEvent.detail*-1;` detail值向下为3

而其他浏览器

`event.originalEvent.wheelDelta` wheelDelta向下为负数(-120)



