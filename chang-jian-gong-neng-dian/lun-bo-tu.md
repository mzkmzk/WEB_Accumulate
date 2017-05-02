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

## 4.2 简单横向滚动

![轮播移动截图](/assets/carousel_2_move.gif)

在线地址: <http://demo.404mzk.com/carousel/2-move/>

简单的滚动其实是所有的图片都在一行

然后通过left或其他方式控制位置实现

实现所有图片都在一行的方式有很多种,例如

html代码


```html
<div class="wrapper">
    <ul class="j_carousel_ul">
        <li><img src="../images/wechat.png"></li>
        <li><img src="../images/sogou.png"></li>
        <li><img src="../images/xmind8.png"></li>
    </ul>
</div>
```

css代码

```css
.wrapper{
    width: 680px;
    overflow: hidden;
 }
ul{
    white-space:nowrap;
}
li{
    display:inline-block;
}

img{
    width: 680px;
    height: 240px;
    
}
```

上面的css控制了比较重要的一步

就是ul是会被裁剪的

![轮播ul显示](/assets/2470298-0f6308aec6e15f44.png)

关于移动的话,这里只做了最简单的切换

```javascript
document.querySelector('.j_carousel_ul').style.marginLeft = '-' + cur_index * 680 +'px';
```
对ul进行marginLeft进行更改

> 注意

这里笔者因为css不太熟,之前一直想left在第一个li里,然后后面的元素自动跟着left,但是发现 后面的元素不能移动

所以left应该是在ul, ul的父元素对ul进行裁剪 

## 4.3 简单纵向移动

![纵向滚动](/assets/carousel_3_cloumn.gif)

在线demo: <http://demo.404mzk.com/carousel/3-column/>

横向是通过margin-left,而纵向则是利用margin-top

```html
<div class="wrapper">
    <ul class="j_carousel_ul">
        <li><img src="../images/wechat.png"></li>
        <li><img src="../images/sogou.png"></li>
        <li><img src="../images/xmind8.png"></li>
    </ul>
</div>
```

css代码

```css
    .wrapper{
        height: 240px;
        overflow: hidden;
    }
   
    ul{
        
    }
    li{
        list-style: none;
 
    }
    img{
        width: 680px;
        height: 240px;
        
    }
```

切换时通过

```javascript
 document.querySelector('.j_carousel_ul').style.marginTop = '-' + cur_index * 210 +'px';
``

## 4.4 循环轮播

![无效轮播demo截图](/assets/carousel_4_infinite.gif)

在线demo: <http://demo.404mzk.com/carousel/4-infinite/>




# 参考链接

1. css裁剪功能: http://www.jianshu.com/p/6e5793760e6e
