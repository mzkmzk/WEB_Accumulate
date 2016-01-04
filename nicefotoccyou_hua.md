# Nicefoto.cc优化

<http://nicefoto.cc/>

![首页加载图](QQ20160104-3.png)

首页加载图中,有3张轮播图,还有其他固定小图片,耗时比较明显,还有加载的时候是先白屏然后由上往下的加载,效果非常恶心.

先安利一下Chrome如何实现不同手机设备和网络环境,和各个浏览器的调试

![Chrome模式](QQ20160104-6.png)

##1. CSS Sprite

最基本和最有用的技巧.

<http://www.cn.spritegen.website-performance.org/>

先把需要的图片放进去,然后里面会有css/less导出,再用相应的class放到图片的位置上.

