# border-radius

# 语法

语法: `border-radius: 1-4 length|% / 1-4 length|%;`

注释：

1. 按此顺序设置每个 radii 的四个值
2. 如果省略 bottom-left，则与 top-right 相同。
3. 如果省略 bottom-right，则与 top-left 相同。
4. 如果省略 top-right，则与 top-left 相同。
5. 不可能省略top-left的
6.  
7.  border-radius 属性是一个简写属性，用于设置四个 border-*-radius 属性。

# border-radius设置的值

可以设成px或百分比,但是设的到底是什么?

![border-radius设置](/assets/corner.png)

上面就相当于`border-radius: 55pt 25pt`

然后`55和25`就确定了一个椭圆的原点还有x轴(55),y轴(25)的长

得出椭圆公式

$$\frac{x^2}{55^2}+\frac{x^2}{25^2} = 1$$

![化椭圆](/assets/200px-Parametric_ellipse.gif)

# 如何画圆

就是四个角都是一个圆,然后就能得出正方形显示成圆形

`border-radius: 50%`(百分比是根据自身的border-box计算的)

# 参考链接

1. http://www.w3school.com.cn/cssref/pr_border-radius.asp
2. https://www.w3.org/TR/css3-background/#the-border-radius
3. https://zh.wikipedia.org/wiki/%E6%A4%AD%E5%9C%86