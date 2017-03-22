# 计算首屏时间

# 环境

1. phantomjs: 2.1.1

# 目标

输入url,可以计算得出

1. DOMContentLoaded回调时间
2. 白屏时间
3. 首屏时间
4. window.onload时间
5. 资源请求数量和大小
6. 加载中的图片比较

# 思路

1. 给page嵌入监听DOMContentLoaded和window.onload
2. 加载页面,不断对页面进行page.renderBase64和page.render
3. 通过page.onLoadStarted、page.onResourceRequested和page.onResourceReceived统计资源个数和大小
4. 页面加载完了之后,把page.renderBase64的图片放进canvas,进行感知哈希算法进行比较相似度

# 计算DOMContentLoaded和window.onload


# 比较图片相似度: 感知哈希算法

1. 将图片缩放到canvas上,大小为8x8px
2. 将图片转为灰度图 ,转换算法为`Y =0.299 * R + 0.587 * G + 0.114 * B`
3. 计算灰度图的hash值: 将每个像素的灰度，与平均值进行比较。大于或等于平均值，记为1；小于平均值，记为0。然后转为16进制(结果为16位的16进制) 
4. 计算两张图片的汉明距离: 两张图的hash值,进行位置间的对比.
5. 计算结果: 如果不相同的数据位不超过5，就说明两张图片很相似；如果大于10，就说明这是两张不同的图片

实现算法文件: 主要是玩弄下canvas: https://github.com/mzkmzk/K-Reptile/blob/master/Src/JS/similar_picture.js

# 参考链接 

1. 感知哈希算法的介绍: http://www.ruanyifeng.com/blog/2011/07/principle_of_similar_image_search.html
2. 感知哈希算法的js实现: http://zhangmhao.github.io/2015/11/16/find-similar-picture/


