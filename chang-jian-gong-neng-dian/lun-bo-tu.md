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
 
```


## 4.4 循环轮播

![无效轮播demo截图](/assets/carousel_4_infinite.gif)

在线demo: <http://demo.404mzk.com/carousel/4-infinite/>

无限轮播的思路主要有两种

1. 不断的clone,然后append到ul的最前面或者最后面
2. 只控制进入视觉的图片,一般为3张

demo采取了方法二

这里没有做任何动画,所以切换图片的时候,直接把3个img标签切换掉

如果需要加动画,就是控制margin-left

然后动画结束,立马切换img的src

```html
<div class="wrapper">
     <a href="javascript:;" class="j_pre">上一张</a>
     <a href="javascript:;" class="j_next" >下一张</a>
    <ul class="j_carousel_ul">
        <li ><img class="j_img_0"></li>
        <li ><img class="j_img_1"></li>
        <li ><img class="j_img_2"></li>
    </ul>
   
    <p>当前第 <em class="j_cur_index">0</em> 张</p>
</div>
```

css代码
```css
    .wrapper{
        user-select:none;
        width: 850px;
        overflow: hidden;
    }
   
    ul{
        padding-left: 80px;
        white-space:nowrap;
    }
    li{
        list-style: none;
        display: inline-block;
  
    }

    img{
        width: 680px;
        height: 240px;
        
    }
```

javascript代码

```javascript
 var cur_index = 0,
        IMAGE_ARRAY = [
            '../images/wechat.png',
            '../images/sogou.png',
            '../images/xmind8.png',
            '../images/fuli.png',
            '../images/watch.jpg'
        ],
        get_right_index = function(index){
            return index < 0 ? IMAGE_ARRAY.length + index : index % IMAGE_ARRAY.length
        },
    change_pic = function(next_index){
         cur_index = get_right_index( next_index )
       
        
         document.querySelector('.j_img_0').src = IMAGE_ARRAY[  get_right_index( cur_index - 1) ];
        document.querySelector('.j_img_1').src = IMAGE_ARRAY[  cur_index ];
        document.querySelector('.j_img_2').src = IMAGE_ARRAY[ get_right_index( cur_index + 1 ) ];

        document.querySelector('.j_cur_index').innerHTML = cur_index
    }
      document.querySelector('.j_carousel_ul').style.marginLeft = '-' + 1 * 680 +'px';
    document.querySelector('.j_img_0').src = IMAGE_ARRAY[ IMAGE_ARRAY.length - 1 ];
    document.querySelector('.j_img_1').src = IMAGE_ARRAY[ 0 ];
    document.querySelector('.j_img_2').src = IMAGE_ARRAY[ 1 ];
    document.querySelector('.j_pre').addEventListener('click',function(){
        change_pic( cur_index - 1 )
    })

    document.querySelector('.j_next').addEventListener('click',function(){
        change_pic( cur_index + 1 )
    })
```

## 4.5 增加滑动功能

![拖动轮播图](/assets/carousel_5_scroll.gif)

在线demo: <http://demo.404mzk.com/carousel/5-scroll/>

实现了4.4中的无限轮播,这里做的主要做的是一个拖动的

开始拖动 = mousedown + mousemove

停止拖动 = mouseup

当然为了兼容各平台,可以参考这个代码

![兼容拖动代码](/assets/QQ20170503-090247.png)

基于4.4的代码,HTML和CSS不需改动,主要是JS

切换图片的函数,增加重新定位功能

在每次切换完图片后都

`document.querySelector('.j_carousel_ul').style.marginLeft = '-' + 1 * 680 +'px'; //纠正滑动距离
`
来纠正拖动完成后的图片显示距离

```javascript
var carousel_ul = document.querySelector('.j_carousel_ul'),
    is_start_move = false,
    carousel_ul_margin_left = -680, //以680位基准
    mousedown_start_x,
    mousedown_callback = function(event){
        event.preventDefault(); //阻止拖动图片的 浏览器默认请 
        console.log('mousedown_callback event' + event.clientX);
        is_start_move = true
        mousedown_start_x = event.clientX
    },
    mousemove_callback = function(event){
        if ( is_start_move ) {
            var move_distance = event.clientX - mousedown_start_x
            if ( move_distance >= 100 ) { //上一张 往右滑
                is_start_move = false;
                change_pic('pre')
            }else if (move_distance <= -100) { //下一张 往左滑
                is_start_move = false;
                 change_pic('next')
            }else { // 平常滑动
                carousel_ul.style.marginLeft = ( carousel_ul_margin_left  +  move_distance ) +'px';
            }
            
            
        }
        
    },
    mouseup_callback = function(event){
        is_start_move = false;

    }

document.querySelector('.j_carousel_ul').addEventListener('mousedown',mousedown_callback)
document.querySelector('.j_carousel_ul').addEventListener('mousemove',mousemove_callback)
document.querySelector('.j_carousel_ul').addEventListener('mouseup',mouseup_callback)

```

# 参考链接

1. css裁剪功能: http://www.jianshu.com/p/6e5793760e6e
2. 元素拖动参考: http://www.zhangxinxu.com/wordpress/2010/03/javascript%E5%AE%9E%E7%8E%B0%E6%9C%80%E7%AE%80%E5%8D%95%E7%9A%84%E6%8B%96%E6%8B%BD%E6%95%88%E6%9E%9C/