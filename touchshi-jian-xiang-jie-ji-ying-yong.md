# touch事件详解及应用

此篇文章主要介绍touch类的事件, 也会顺带介绍下mouse类的, 因为比较类似

# 分类

touch类和mouse类的时间主要有

> touch 事件

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
3. 

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



