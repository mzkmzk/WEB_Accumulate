# 从输入URL到找性能优化点

有很多面试官都会问从输入URL到页面展示出来的过程是怎么样的?

过程本身没有太大意义

意义在于 我们能不能从这个过程里找到性能优化点

# 全局过程

# DNS

假设现在要解析http://www.xunlei.com

-> 发起http://www.xunlei.com请求

-> 查找浏览器DNS缓存 (可  `chrome://net-internals/#dns`或`chrome://dns/` 查看 DNS缓存情况)

-> hosts文件查找

-> 路由器中查找 (不一定每个路由器都有这个功能)

-> ISP(Internet Service Provider 中国电信、联通等)DNS服务器 或 系统设置的DNS服务器中 (例如8.8.8.8) 的缓存中查找

-> DNS服务器 分级查询域名

### 浏览器的DNS缓存 

chrome://net-internals/#dns

![dns缓存](/assets/QQ20180716-124635.png)

这里主要记录域名的IP 和过期时间 TTL这里显示以毫秒为单位 (async DNS configuration 和 最后列的network changes 未研究透彻)

另外还有 chrome://dns/ (用隐身模式打开会显示 DNS pre-resolution and TCP pre-connection is disabled.)

主要根据 域名访问的次数 来选取下次启动chrome时 prefetch的NDS 

Future startups will prefetch DNS records for 10 hostnames

代表这次启动chrome的时候 预DNS解析的10个域名(根据上次关闭chrome时 访问量最大的10个域名)

![prefetch dns](/assets/QQ20180716-125120.png)



拿chrome

### CNAME优化

https://www.cloudxns.net/Support/detail/id/1937.html

遗留:


chrome://dns/ 和 chrome://net-internals/#dns图表的解释

TTL的含义

https://blog.csdn.net/zhanghaiyang9999/article/details/39505861

traceroute

# NS记录

# 浏览器加载资源的顺序

# chrome devtools 的network

https://developers.google.com/web/tools/chrome-devtools/network-performance/reference