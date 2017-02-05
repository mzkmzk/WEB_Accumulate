# 关于userAgent

# 背景

远古时代,各大浏览器瞎鸡*折腾,在自己的userAgent里加入其它内核的信息,为了别人识别不出它来.

# 我们需要什么

## 所处软件及其版本号

在国内,有时需要区分的是在微信还是在支付宝里,幸好,其userAgent有声明

微信: `mozilla/5.0 (iphone; cpu iphone os 5_1_1 like mac os x) applewebkit/534.46 (khtml, like gecko) mobile/9b206 micromessenger/5.0`中的micromessenger/5.0


支付宝: ` Mozilla/5.0 (Linux; U; Android 5.1.1; en-us; KIW-AL10 Build/HONORKIW-AL10) AppleWebKit/534.30 (KHTML, like Gecko) Version/4.0 UCBrowser/1.0.0.100 U3/0.8.0 Mobile Safari/534.30 AlipayDefined(nt:WIFI,ws:360|592|3.0) AliApp(AP/9.5.3.030408) AlipayClient/9.5.3.030408 Language/zh-Hans`中的AlipayClient/9.5.3.030408

## 所在系统

这样有些人貌似会用来设置系统来设置字体,但本人没试过

## 区分终端

pc/pad/wap

## 区分内核



# 可以获取什么

可以稍微看下

1. https://techblog.willshouse.com/2012/01/03/most-common-user-agents/
2. https://deviceatlas.com/blog/list-of-user-agent-strings

第一条是很远古时代的浏览器都有的,那时候真是混沌!

第二条则是比较现代化的,各自厂商的userAgent已经工整了很多

# 参考资料

1. https://gist.github.com/wjp2013/fff34c063cf0cf227d65
