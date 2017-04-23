# hr标签

# 简介

制作一条水平线

`<hr/>`在各浏览器的显示常常是100%的宽度，2px的高度,3D边框风格渲染

# 粗细

在大部分浏览器中2px的高度

是因为height为0

而border四边都是1

所以大部分浏览器的hr默认为2px

所以如果你只想要1px 可以这么做

```css
hr{
    height: 1px;
    border: none;
    background-color: 颜色;//必须设,不然没有任何东西显示在IE其他的浏览器
    color: 颜色;//IE设置hr颜色的方法
}
```

或者改变border

# 颜色

比较兼容的写法

```css
hr{
    color:red;
    background-color:red;
    height:1px;
    line-height:1px;/*保证ＩＥ下高度效果一致，不留白色空白区*/
    font-size:0;/*设置为0，以确保IE6下不留空白区*/
    border:none;
}
著作权归作者所有。
商业转载请联系作者获得授权,非商业转载请注明出处。
原文: https://www.w3cplus.com/css/css-hr © w3cplus.com
```

# 参考链接

1. https://www.w3cplus.com/css/css-hr