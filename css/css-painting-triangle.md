# CSS画三角形

# 如何画三角形

- 定义一个四边形
- 定义border
- 设定四边形的`width: 0`, `height: 0`
- 此时会得到3个三角形 隐藏另外两个即可(tips: 三角形对面的border可以不设定)

<video width="80%" controls>
    <source src="https://mzk-blog-static.oss-cn-shenzhen.aliyuncs.com/study-css-demo/QQ20201113-164907-HD.mp4" type="video/mp4">
</video>

基本的实现代码

![基本实现css三角形代码](https://mzk-blog-static.oss-cn-shenzhen.aliyuncs.com/study-css-demo/QQ20201116-153333.png)


[体验链接](http://study-css-demo.404mzk.com/effect/triangle/)

# 如何画空心三角形

原理是 通过`:after`再制作一个同样的三角形 设置为白色 盖住原本的三角形

![空心三角形](https://mzk-blog-static.oss-cn-shenzhen.aliyuncs.com/study-css-demo/QQ20201116-090352.png)

这里`:after`里三角形为什么是left`-100px`

因为 回想三角形的制作原理, 是通过`width`和`height`为0, 所以中心点是三角形的尖端

[体验地址](http://mzk-study-css-demo.404mzk.com/effect/triangle/hollow/)

# 参考资料

- https://css-tricks.com/snippets/css/css-triangle/
- https://stackoverflow.com/questions/7073484/how-do-css-triangles-work/7073503#7073503