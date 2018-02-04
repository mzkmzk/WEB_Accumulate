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

1. clientX: 视口中的x坐标
2. clientY: 视口中的y坐标
3. pageX: 页面中的x坐标
4. pageY: 页面中的y坐标
5. screenX: 屏幕中的x坐标
6. screenY: 屏幕中的Y坐标

# 应用

## 判断各种手势



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