# 轮播图

# 1. 简介

轮播图可以抽象为通过改变坐标位置,而使显示内容得以变化

# 2. 附属功能

因为的交互方式有

1. 手势滑动轮播
2. 点击左右切换图片
3. 总共多少张轮播
4. 当前第几张轮播
5. 循环轮播
6. 自动轮播 鼠标hover不轮播

# 3. 表现形式

1. 左右图片滚动切换
2. 图片闪烁切换

# 4. 例子

## 4.1 简单的图片切换

![图片切换轮播图](/assets/carousel_1_base.gif)

在线地址<http://demo.404mzk.com/carousel/1-base/>

这种比较简单,就是用js控制src就OK...

## 4.2 简单滚动

简单的滚动其实是所有的图片都在一行

然后通过left或其他方式控制位置实现

实现所有图片都在一行的方式有很多种,例如

```html
<ul>
    <li><img src="../images/wechat.png"></li>
    <li><img src="../images/sogou.png"></li>
    <li><img src="../images/xmind8.png"></li>
</ul>
```

```css
ul{
    white-space:nowrap;
    width: 680px;
    overflow: hidden;
}
li{
    display:inline-block;
}

img{
    width: 680px;
    height: 240px;
    
}
```


