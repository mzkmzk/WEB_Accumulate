# 布局相关

# 满幅的背景, 定宽的内容

## margin:auto实现

最常见的写法是

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