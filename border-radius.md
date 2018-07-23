# border-radius

# 语法

语法: `border-radius: 1-4 length|% / 1-4 length|%;`

注释：

1. 按此顺序设置每个 radii 的四个值
2. 如果省略 bottom-left，则与 top-right 相同。
3. 如果省略 bottom-right，则与 top-left 相同。
4. 如果省略 top-right，则与 top-left 相同。
5. 不可能省略top-left的
6.  border-radius 属性是一个简写属性，用于设置四个 border-*-radius 属性。


# border-radius设置的值

可以设成px或百分比,但是设的到底是什么?

![border-radius设置](/assets/corner.png)

上面就相当于`border-radius: 55pt 25pt`

然后`55和25`就确定了一个椭圆的原点还有x轴(55),y轴(25)的长

得出椭圆公式

$$\frac{x^2}{55^2}+\frac{x^2}{25^2} = 1$$

![化椭圆](/assets/200px-Parametric_ellipse.gif)

在用这个属性的时,记住4个椭圆的位置和相邻两个椭圆会防重叠即可

# 如何画圆

就是四个角都是一个圆,然后就能得出正方形显示成圆形

`border-radius: 50%`(百分比是根据自身的border-box计算的)

# 椭圆能影响的部分

如果四个角都设置了radius

会形成内部四个椭圆,但每个椭圆只能影响自己所在的角的部分

例如`border-top-left-radius`只能影响左上部分

# 防重叠

当任意两个相邻椭圆的半径纸盒超过了border-box的尺寸时,浏览器会按比例减少各个边框半径所使的值,直到他们不会相互重叠位置

(注意,左上和右下不算相邻,右上和左下也不算相邻)

这个其实懂个四个原理后,很容易躲避

# 最大能隐藏的面积

根据前面的结论

圆是可以隐藏最多的情况

还有相邻防重叠原因,只设置左上和右下的

例子

![radius最小面积](/assets/QQ20170107-1.png)

https://jsfiddle.net/404_K/6edc5ja0/3/

假设边长为2

图3的面积 = ( 4分之一个圆 - 2分之一个正方形 ) x 2 = π - 2

图1,2,4的面积都为π

所以是图3的情况是能隐藏最多面积的

# 参考链接

1. http://www.w3school.com.cn/cssref/pr_border-radius.asp
2. https://www.w3.org/TR/css3-background/#the-border-radius
3. https://zh.wikipedia.org/wiki/%E6%A4%AD%E5%9C%86
4. http://cssreference.io/property/border-radius/