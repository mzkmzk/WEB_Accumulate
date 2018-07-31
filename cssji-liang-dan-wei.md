# CSS计量单位

# 与font-size相关的单位

ex:  1为 小写x的高度

em: 可以理解为 一个父元素中 font-size的倍数 不一定的话chrome默认为16px 其高度值可以理解为正方形的方格的边长

rem: 跟em类似 但其的参考标准为html标签的font-size (IE9及其以上浏览器可用, 但是IE9表现有点奇葩)

ch: 1ch = 0的宽度 

# 与viewpoint相关的单位 

vh: 1vh = 1/100的视口高度

vw: 1vw = 1/ 100的视口宽度

vmin: 1vmin = 设备宽度 > 设备高度 ? 1vh : 1vw

vmax: 1vmax = 设备宽度 > 设备高度 ? 1vw : 1vh



# 计量单位的运用

### rem和vw的结合

在移动侧 因为移动设备尺寸不一 所以需要对不同尺寸的设备做不一样的大小处理

一般的处理方式是 

```css
html{ font-size: xxxxvw }

.xxx{ padding: aaarem }
```

# 参考链接

1. https://github.com/simaQ/cssfun/issues/1