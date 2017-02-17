# mousewheel

监听滚动事件

# 一般写法

```javascript
 $(选择器).on("mousewheel DOMMouseScroll",function(event){
 }
```
mousewheel为通用的滚轮事件

而DOMMouseScroll是Fixfox的滚轮方式

# 滚轮方向

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

# 触发频率

在chrome监听 mousewheel 并且打上毫秒,并且鼠标不停的滚动

> 鼠标下 

```shell
mousewheel46 s:106
mousewheel46 s:118
mousewheel46 s:130
mousewheel46 s:140
mousewheel46 s:149
mousewheel46 s:159
mousewheel46 s:173
mousewheel46 s:474
mousewheel46 s:497
mousewheel46 s:499
mousewheel46 s:510
mousewheel46 s:530
mousewheel46 s:565
mousewheel46 s:592
mousewheel46 s:611
mousewheel46 s:642

```
不断滚动时,几乎10来毫秒就会被触发一次,而且鼠标滚轮停止,事件立马停止

> Mac触摸板下

大概如果在触摸板最下面,一下滑到最下面

```shell
mousewheel4 s:337
mousewheel4 s:346
mousewheel4 s:350
mousewheel4 s:361
mousewheel4 s:366
mousewheel4 s:377
...
...
mousewheel5 s:363
mousewheel5 s:380
mousewheel5 s:397
mousewheel5 s:430
mousewheel5 s:446
mousewheel5 s:480
```

会触发N多次,而且手指离开后,仍然会继续触发滑动,持续事件大概1~2秒

