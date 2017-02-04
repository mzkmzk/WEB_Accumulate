# 关于userAgent

# 背景

远古时代,各大浏览器瞎鸡*折腾,在自己的userAgent里加入其它内核的信息,为了别人识别不出它来.

# 我们需要什么

## 所处软件

在国内,有时需要区分的是在微信还是在支付宝里,幸好,其userAgent有声明

微信: `mozilla/5.0 (iphone; cpu iphone os 5_1_1 like mac os x) applewebkit/534.46 (khtml, like gecko) mobile/9b206 micromessenger/5.0`中的micromessenger/5.0


支付宝

# 可以获取什么

可以稍微看下

1. https://techblog.willshouse.com/2012/01/03/most-common-user-agents/
2. https://deviceatlas.com/blog/list-of-user-agent-strings

第一条是很远古时代的浏览器都有的,那时候真是混沌!

第二条则是比较现代化的,各自厂商的userAgent已经工整了很多

# 参考资料

1. https://gist.github.com/wjp2013/fff34c063cf0cf227d65
