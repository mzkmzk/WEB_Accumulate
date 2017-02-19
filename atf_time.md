# 首屏加载时间

# 定义

首屏时间

1. 页面上有大概的样子显示出来(不包含图片)
2. 首屏的所有资源加载完毕

# 页面形式多样

1. SPA: DOM一开始只有一个root标签,之后的DOM是根据JS渲染的
2. 普通页面: 就是常规的静态页面

# 计算方式

1. 打点: 最常见和最较为准确的方式,能够根据不同的页面形式,定义自己所需要的首屏时间
2. 模拟工具: 通过 检测网页的图像

# 参考资源

1. 首屏打点的技巧: http://www.alloyteam.com/2016/01/points-about-resource-loading/
2. 介绍了相关的自动化工具: http://fex.baidu.com/blog/2014/05/build-performance-monitor-in-7-days/
3. 较全面的工程化体系: http://velocity.oreilly.com.cn/2013/ppts/front-end_performance_optimization_in_baidu.pdf