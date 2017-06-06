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

# 触屏版和鼠标的节流

一般在pc上做翻屏功能

![滑轮](/assets/QQ20170217-0.png)

我们希望用户滚一次滑轮,翻转一页

所以在pc上刚开始是做了500毫秒的节流,就是触发一次滑屏,然后500ms后不接受到滚轮事件不触发翻页

但是刚在Mac触摸板滑一次屏 最多能触发2s的滚轮事件

所以会触发2~3次翻页,这是我们不愿意看到的

但是如果把节流时间加长,鼠标则会受影响(连续滚动鼠标会有延迟感)

所以js目前貌似不能判断书触摸板还是鼠标,所以这个问题笔者暂未解决

> tips

网上(https://www.zhihu.com/question/46620479)说

`event.originalEvent.sourceCapabilities.firesTouchEvents`能判断是否为触摸行为,但是笔者的Mac无论鼠标还是触摸板都是false,可能说的是直接触屏?而不是触摸板

# 兼容性

FF绑定: window.addEventListener('DOMMouseScroll', jieliu, false);

其他高级浏览器绑定: window.addEventListener('mousewheel', jieliu, false);

IE8绑定: document.body.onmousewheel = jieliu 或者  document.body.attachEvent('onmousewheel', jieliu) //不能绑定到window

# 参考链接

1. mousewhell基本属性: http://www.zhangxinxu.com/wordpress/2013/04/js-mousewheel-dommousescroll-event/