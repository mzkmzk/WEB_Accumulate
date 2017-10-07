# 布局相关

# 满幅的背景, 定宽的内容

![满幅的背景, 定宽的内容](/assets/full-background-fixation-width.png)

## margin:auto实现

最常见的写法是

http://demo.404mzk.com/css/layout/full-background-fixation-width.html#demo_1

css部分:

```css
.demo_div_1 {
    background: orange;
}
.demo_wrapper {
    max-width: 900px;
    margin: auto;
}
```

html部分: 

```html
<div class="demo_div_1">
    <div class="demo_div_1_wrapper">
         <h1>Hi I am demo</h1>
         <h3>mzk</h3>
    </div>
</div>
```

这里关键的是margin:auto 浏览器究竟做了什么

1. 在高度上无作为
2. 在宽度上, 如果父元素有宽带限制则marin都为0, 如果无宽带限制则左右边距都等于`视口宽度的一半减去的内容宽带的一半`

## calc+padding实现

http://demo.404mzk.com/css/layout/full-background-fixation-width.html#demo_2

css部分

```css
.demo_div_2 {
    background: orange;
    padding: 0 calc(50% - 450px)
}
```

html部分

```html
<div class="demo_div_2">
     <h1>Hi I am demo</h1>
        <h3>mzk</h3>
</div>
```

450px其实就是限定可宽度最高为900px
