# border-radius

# 语法

语法: `border-radius: 1-4 length|% / 1-4 length|%;`

注释：

1. 按此顺序设置每个 radii 的四个值
2. 如果省略 bottom-left，则与 top-right 相同。
3. 如果省略 bottom-right，则与 top-left 相同。
4. 如果省略 top-right，则与 top-left 相同。
5. 不可能省略top-left的


* border-radius 属性是一个简写属性，用于设置四个 border-*-radius 属性。

# border-radius设置的值

可以设成px或百分比,但是设的到底是什么?

![border-radius设置](/assets/corner.png)

上面左上角就相当于`border-top-left-radius: 55pt 25pt`

# 参考链接

1. http://www.w3school.com.cn/cssref/pr_border-radius.asp
2. https://www.w3.org/TR/css3-background/#the-border-radius