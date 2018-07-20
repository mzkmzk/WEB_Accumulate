# background

# background-position

background-position的值 主要有几种类型 数字 百分比 或者 left|top|right|bottom|center 

基本语法可以理解为 

background-postion: positionX positionY

如果只有一个值被指定，则这个值就会默认设置背景图片位置中的水平方向，与此同时垂直方向的默认值被设置成50%。

比较特殊的是 当值为百分比的计算方式 

positionX = ( 容器的宽带 - 图片的宽度 ) * percentX

positionY = ( 容器的高度 - 图片的高度 ) * percentY

这里引入下MDN的一个图

![backgroun-postion](/assets/QQ20180720-183836.png)

# 参考链接

1. MDNbackground-postion的说明: https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-position


