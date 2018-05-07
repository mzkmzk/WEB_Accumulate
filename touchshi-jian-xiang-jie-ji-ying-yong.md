# touch事件详解及应用

此篇文章主要介绍touch类的事件, 也会顺带介绍下mouse类的, 因为比较类似

# 分类

touch类和mouse类的时间主要有

> touch 事件

touch事件初体验: http://demo.404mzk.com/event/base/window_all_type.html

1. touchstart: 手指触摸屏幕时触发
2. touchmove: 手指滑动屏幕时触发
3. touchend: 手指离开屏幕时触发
4. touchcancel: 手指触摸屏幕期间内, 由于手机电话、按HOME健离开浏览器时触发

> touch事件对象包含属性

![touchstart触发类型](/assets/WX20180204-172400.png)

上图为ios safari下的console

1. touches: 当前屏幕上的手指对象数组
2. targetTouches: 当前绑定元素上的手指对象数组
3. changeTouches: 状态发生改变的手指对象数组

这几个数组里的对象包含比较重要的属性有

1. clientX: 触摸目标在视口中的x坐标
2. clientY: 触摸目标在视口中的y坐标
3. pageX: 触摸目标在页面中的x坐标
4. pageY: 触摸目标在页面中的y坐标
5. screenX: 触摸目标在屏幕中的x坐标
6. screenY: 触摸目标在屏幕中的Y坐标

# 应用

## 判断各种手势

手势判断初体验: http://demo.404mzk.com/event/base/alloy_finger.html

这里按照讲解AlloyFinger源码的思路来解析各种手势

AlloyFinger在构造函数中监听元素移动

```javascript
var AlloyFinger = function (el, option) {
    ...
    this.element.addEventListener("touchstart", this.start, false);
    this.element.addEventListener("touchmove", this.move, false);
    this.element.addEventListener("touchend", this.end, false);
    this.element.addEventListener("touchcancel", this.cancel, false);
    ...
}
```

比如在轮播图中, 要判断用户向左滑还是向右滑

> tap

在移动端里, 如果监听click时间, 会有300ms的延迟时间\(因为浏览器需要判断该次点击是否为其他操作\)

![移动端tap](/assets/687474703a2f2f696d61676573323031352e636e626c6f67732e636f6d2f626c6f672f3130353431362f3230313631312f3130353431362d32303136313131313039353930363034352d3733333935373734312e706e67.png)

移动端click有300毫秒延时，tap的本质其实就是touchend。但是要判断touchstart的手的坐标和touchend时候手的坐标x、y方向偏移要小于30。小于30才会去触发tap

```javascript
 AlloyFinger.prototype = {
     start: function(evt){
         this.x1 = evt.touches[0].pageX;
         this.y1 = evt.touches[0].pageY;
         //防止是长按 或滑动等其他操作         
         this._preventTap = false;
         this.longTapTimeout = setTimeout(function () {
             this.longTap.dispatch(evt, this.element);
             this._preventTap = true;
         }.bind(this), 750);
     },
     move: function(evt){
         var currentX = evt.touches[0].pageX,
             currentY = evt.touches[0].pageY;

         this.x2 = currentX;
         this.y2 = currentY;
     },
     end: function(evt){
         var self = this;
         if ((this.x2 && Math.abs(this.x1 - this.x2) > 30) ||
            (this.y2 && Math.abs(this.y1 - this.y2) > 30)) {
           ...
         } else {
             if(!self._preventTap){
                 //触发tap
                 self.tap.dispatch(evt, self.element);
             }
         }
     }
 }
```

> longtips

![logtips](/assets/687474703a2f2f696d61676573323031352e636e626c6f67732e636f6d2f626c6f672f3130353431362f3230313631312f3130353431362d32303136313131313039353931383031342d3831373832373339332e706e67 %281%29.png)

touchstart开启一个750毫秒的settimeout，如果750ms内有touchmove或者touchend都会清除掉该定时器。超过750ms没有touchmove或者touchend就会触发longTap

```javascript
AlloyFinger.prototype = {
     start: function(evt){
         this.longTapTimeout = setTimeout(function () {
             //触发长按tips
             this.longTap.dispatch(evt, this.element);
         }.bind(this), 750);
     },
     move: function(evt){
         clearTimeout(this.longTapTimeout);
     },
     end: function(evt){
         clearTimeout(this.longTapTimeout);
     }
 }

```

> swipe滑动

![swipe滑动](/assets/687474703a2f2f696d61676573323031352e636e626c6f67732e636f6d2f626c6f672f3130353431362f3230313631312f3130353431362d32303136313131313039353932323838392d313439323133343934382e706e67.png)



触发此事件的因素有

1. touchstart的手的坐标和touchend时候手的坐标x、y方向偏移要大于30
2. 判断x坐标的变化大还是y坐标的变化大, 假如x坐标变化大则为向左或右, 假如y坐标变化大则向上或向下
3. 通过判断touchstart的pageX和最后的touchmove的pageX 对比大小, 判断向左还是向右,向上向下同理

```javascript
AlloyFinger.prototype = {
     start: function(evt){
         this.longTapTimeout = setTimeout(function () {
             //触发长按tips
             this.longTap.dispatch(evt, this.element);
         }.bind(this), 750);
     },
     move: function(evt){
         this.x2 = evt.touches[0].pageX;
         this.y2 = evt.touches[0].pageY;
     },
     end: function(evt){
         if ((this.x2 && Math.abs(this.x1 - this.x2) > 30) ||
            (this.y2 && Math.abs(this.y1 - this.y2) > 30)) {
            evt.direction = this._swipeDirection(this.x1, this.x2, this.y1, this.y2);
            this.swipeTimeout = setTimeout(function () {
                self.swipe.dispatch(evt, self.element);

            }, 0)
        }
     },
     _swipeDirection: function (x1, x2, y1, y2) {
        return Math.abs(x1 - x2) >= Math.abs(y1 - y2) ? (x1 - x2 > 0 ? 'Left' : 'Right') : (y1 - y2 > 0 ? 'Up' : 'Down')
    }
 }

```

## 拖动div

拖动初体验: http://demo.404mzk.com/event/base/move_div.html

1. 阻止preventDefault事件主要防止拖动时 浏览器跟着滑动和微信页面向下滑动
2. 将pageX和pageY减去div的一半是为了让物体置于手指中间
3. 判断pageX和pageY最后的最大和最小值, 为了防止拖动出屏幕

```javascript
var div = document.querySelector('.j_demo_div'),
    half_width = div.offsetWidth / 2,
    half_height = div.offsetHeight / 2,
    min_x = 0,
    min_y = 0,
    max_x = window.innerWidth - div.offsetWidth,
    max_y = window.innerHeight - div.offsetHeight
    
div.addEventListener('touchmove', function(event){
    event.preventDefault()
    var x = event.touches[0].pageX - half_width,
        y = event.touches[0].pageY - half_height

    if ( x < min_x ) {
        x = min_x
    }else if ( x > max_x){
        x = max_x
    }

    if ( y < min_y ) {
        y = min_y
    }else if ( y > max_y){
        y = max_y
    }
    div.style.left = x  + 'px'
    div.style.top = y + 'px'
    
})
```

## 下拉加载更多

下拉加载更多 需要实现的点有

1. 下拉的过程中列表和刷新提示向下移动
2. 滑到一定距离后 出现 松开可刷新 并且箭头翻转
3. 手指离开屏幕 判断距离是否大于指定距离 大于则进行刷新

```javascript
 var scroller = document.querySelector("#scroller"),
        arrow = document.querySelector("#arrow"),
        pull_refresh = document.querySelector("#pull_refresh"),
        list = document.querySelector("#list"),
        index = 0;

    Transform(pull_refresh, true);
    Transform(scroller, true);


    new AlloyTouch({
        touch: "#wrapper",//反馈触摸的dom
        vertical: true,//不必需，默认是true代表监听竖直方向touch
        target: scroller, //运动的对象
        property: "translateY",  //被滚动的属性
        initialValue: 0,
        min: window.innerHeight - 45 - 48 - 2000, //不必需,滚动属性的最小值
        max: 0, //不必需,滚动属性的最大值
        change: function (value) {
            pull_refresh.translateY = value;
        },
        touchMove: function (evt, value) {
            if (value > 70) {
                //http://caniuse.com/#search=classList
                arrow.classList.add("arrow_up");
            } else {
                arrow.classList.remove("arrow_up");
            }
        },
        touchEnd: function (evt, value) {
            if (value > 70) {
                this.to(60);
                mockRequest(this);
                return false;
            }
        }
    })

    function mockRequest(at) {
        pull_refresh.classList.add("refreshing");
        setTimeout(function () {
            var i = 0,
                len = 3;
            for (; i < len; i++) {
                var li = document.createElement("li");
                li.innerHTML = "new row " + index++;
                list.insertBefore(li, list.firstChild);
            }
            arrow.classList.remove("arrow_up");
            pull_refresh.classList.remove("refreshing");
            pull_refresh.translateY = 0;
            at.to(at.initialValue);
            at.min -= 40 * 3;
        }, 500);
    }
```

DEMO示例: http://alloyteam.github.io/AlloyTouch/refresh/pull_refresh/


# 注意事项

> 阻止缩放

在双指操控屏幕、连续双击屏幕等操作下, 很多浏览器会有默认的缩放或方法页面的效果

所以有冲突的情况下 可以阻止网页缩放

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
```

具体说明

```
width             - viewport的宽度 
height            - viewport的高度
initial-scale     - 初始的缩放比例
minimum-scale     - 允许用户缩放到的最小比例
maximum-scale     - 允许用户缩放到的最大比例
user-scalable     - 用户是否可以手动缩放
```

> android4.x 浏览器touchend的bug

android4.x 默认touchstart后只触发一次touchmove 并且不触发touchend

只能在touchstart或touchmove 里event.preventDefault() 才能继续触发多次touchmove和touchend

但是要注意的是: 不建议在documet.body上绑定touchmove然后`event.preventDefault()`, 因为会阻止浏览器滚动

一般针对需要处理的div , 然后在其下监听touchmove 去`event.preventDefault()`

```javascript
var is_android_4 = navigator.userAgent.indexOf('Android 4') !== -1
document.querySelector('.dialog').addEventListener('touchmove',function(e){
    if(is_android_4)
    e.preventDefault();
   
})
```

> ios 11 addEventListener的新限制

如果你出现了 evnet.preventDefault() 无法阻止 浏览器原生事件, 可能掉进这个新坑里了

<http://blog.404mzk.com/yi-dong-duan-de-keng-keng-wa-wa.html#ios>

## 下拉刷新及 下拉加载更多

待补充 对Alloytouch的应用

# 参考链接

1. aloyfiger原理详解: https://github.com/AlloyTeam/AlloyCrop/blob/master/asset/alloyfinger.md
2. android4.x bug: http://www.cnblogs.com/shuiyi/p/5138006.html

