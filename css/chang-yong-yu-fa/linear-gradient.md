# linear-gradient

# 思考点

- 如何控制渐变的方向
- 如何制作混合渐变

# 如何控制渐变方向

渐变的效果由渐变线和渐变的起点与终点所决定

渐变的方向由一条渐变线所决定

渐变线由下面几点决定

- 中点[中点是个数学概念](https://zh.wikipedia.org/wiki/%E4%B8%AD%E9%BB%9E)
- 角度: 由css属性设置

渐变的起点和终点为(渐变线)和(渐变线与图形角的中垂线)的交点

如图所示

![https://mzk-blog-static.oss-cn-shenzhen.aliyuncs.com/study-css-demo/QQ20201110-183640.png]()

渐变线的方向为由角度所决定

角度为0是表示时钟12点的方向, 然后角度增加是顺时针的

默认的角度为180deg

类似于这个图的形式

![https://mzk-blog-static.oss-cn-shenzhen.aliyuncs.com/study-css-demo/QQ20201111-122606.png]()

[体验角度变化demo](http://study-css-demo.404mzk.com/css3/linear-gradient/base/)

体验demo截图

![https://mzk-blog-static.oss-cn-shenzhen.aliyuncs.com/study-css-demo/QQ20201111-123927.png]()

# 参考资料

- https://www.runoob.com/css3/css3-gradients.html
- https://developer.mozilla.org/zh-CN/docs/Web/CSS/linear-gradient
- https://www.zhangxinxu.com/wordpress/2013/09/%E6%B7%B1%E5%85%A5%E7%90%86%E8%A7%A3css3-gradient%E6%96%9C%E5%90%91%E7%BA%BF%E6%80%A7%E6%B8%90%E5%8F%98/?shrink=1
- https://www.w3cplus.com/css3/do-you-really-understand-css-linear-gradients.html