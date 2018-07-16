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

-> DNS服务器缓存查找( DNS的服务器可能是 ISP(Internet Service Provider 中国电信、联通等) 或 系统设置的DNS服务器中 (例如8.8.8.8) 或路由器设置的DNS服务器 或本机(例如使用dig命令时)) 

-> DNS服务器 分级查询域名

### 浏览器的DNS缓存 

拿chrome来说

chrome://net-internals/#dns

![dns缓存](/assets/QQ20180716-124635.png)

这里主要记录域名的IP 和过期时间 TTL这里显示以毫秒为单位 (async DNS configuration 和 最后列的network changes 未研究透彻)

另外还有 chrome://dns/ (用隐身模式打开会显示 DNS pre-resolution and TCP pre-connection is disabled.)

主要根据 域名访问的次数 来选取下次启动chrome时 prefetch的NDS 

Future startups will prefetch DNS records for 10 hostnames

代表这次启动chrome的时候 预DNS解析的10个域名(根据上次关闭chrome前 访问量最大的10个域名)

![prefetch dns](/assets/QQ20180716-125120.png)

###  DNS 服务器 分级查询域名

可以通过dig命令来查询 例如解析www.xunlei.com

-> 查找`.`下一级的域名查找服务器

-> 查看`com.`下一级的域名查找服务器

-> 查找`xunlei.com.`下一级的域名查找服务器

-> 查找到www.xunlei.com.的域名服务器

```bash
dig +trace www.xunlei.com

; <<>> DiG 9.8.3-P1 <<>> +trace www.xunlei.com
;; global options: +cmd
.			175147	IN	NS	f.root-servers.net.
.			175147	IN	NS	j.root-servers.net.
.			175147	IN	NS	a.root-servers.net.
.			175147	IN	NS	c.root-servers.net.
.			175147	IN	NS	e.root-servers.net.
.			175147	IN	NS	b.root-servers.net.
.			175147	IN	NS	d.root-servers.net.
.			175147	IN	NS	l.root-servers.net.
.			175147	IN	NS	m.root-servers.net.
.			175147	IN	NS	g.root-servers.net.
.			175147	IN	NS	i.root-servers.net.
.			175147	IN	NS	h.root-servers.net.
.			175147	IN	NS	k.root-servers.net.
;; Received 508 bytes from 192.168.8.24#53(192.168.8.24) in 25 ms

com.			172800	IN	NS	a.gtld-servers.net.
com.			172800	IN	NS	b.gtld-servers.net.
com.			172800	IN	NS	c.gtld-servers.net.
com.			172800	IN	NS	d.gtld-servers.net.
com.			172800	IN	NS	e.gtld-servers.net.
com.			172800	IN	NS	f.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
com.			172800	IN	NS	h.gtld-servers.net.
com.			172800	IN	NS	i.gtld-servers.net.
com.			172800	IN	NS	j.gtld-servers.net.
com.			172800	IN	NS	k.gtld-servers.net.
com.			172800	IN	NS	l.gtld-servers.net.
com.			172800	IN	NS	m.gtld-servers.net.
;; Received 492 bytes from 192.203.230.10#53(192.203.230.10) in 215 ms

xunlei.com.		172800	IN	NS	ns1.xunlei.net.
xunlei.com.		172800	IN	NS	ns2.xunlei.net.
xunlei.com.		172800	IN	NS	ns3.xunlei.net.
xunlei.com.		172800	IN	NS	ns4.xunlei.net.
xunlei.com.		172800	IN	NS	ns5.xunlei.net.
;; Received 212 bytes from 192.35.51.30#53(192.35.51.30) in 268 ms

www.xunlei.com.		1800	IN	CNAME	vip1.images.client.xunlei.com.
vip1.images.client.xunlei.com. 1800 IN	A	183.60.209.24
vip1.images.client.xunlei.com. 1800 IN	A	180.97.157.249
xunlei.com.		1800	IN	NS	ns1.xunlei.net.
xunlei.com.		1800	IN	NS	ns5.xunlei.net.
xunlei.com.		1800	IN	NS	ns4.xunlei.net.
xunlei.com.		1800	IN	NS	ns3.xunlei.net.
xunlei.com.		1800	IN	NS	ns2.xunlei.net.
;; Received 277 bytes from 180.97.157.20#53(180.97.157.20) in 24 ms
```

这里可能会有几个疑问

> 如何拿到根域名服务器的查询地址

例如上面的  

`.			175147	IN	NS	f.root-servers.net.`

DNS服务器要怎么去找`f.root-servers.net.`的IP呢

> 为什么根服务器只有13个IP?

受限于UDP包大小(512) 只能容下13组响应地址

在 Internet 数据传输中，UDP 数据长度控制在 576 字节（Internet 标准 MTU 值），

而在许多 UDP 应用程序设计中数据包被限制成 512 字节或更小。这样可以防止数据包的丢失。

具体的计算细节后面再研究 

参考: https://jaminzhang.github.io/dns/The-Reason-of-There-Is-Only-13-DNS-Root-Servers/

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