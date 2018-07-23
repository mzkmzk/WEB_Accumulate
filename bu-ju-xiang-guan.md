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
2. 在宽度上, 如果父元素有宽度限制则marin都为0, 如果无宽带限制则左右边距都等于`视口宽度的一半减去的内容宽度的一半`

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

`50% - 450px`其实就是限定可宽度最高为900px

# 垂直居中

44年前我们就把人类送上月球了, 但现在我们仍然无法再CSS中实现垂直居中 --James Anderson

## 基于绝对定位的解决方案1 绝对定位+margin

http://demo.404mzk.com/css/layout/vertical-centering.html#demo_1

```css
.demo {
    background: orange;
    position: relative;
    height: 300px;
}
.demo_div_1 {
    background: #655;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    /**
     * transform可改为
     * margin-top -3em;
     * margin-left: -9em;
     * width: 18em;
     * height: 6em;
     * 
     */
    }
```

缺点: 

1. 要试用绝对定位

## 基于Flex

http://demo.404mzk.com/css/layout/vertical-centering.html#demo_2

```css
.demo_2{
    background: orange;
    display: flex;
    align-items: center;
    justify-content: center;
    height: 300px;
}
.demo_div_2 {
   background: #655;
}
```
