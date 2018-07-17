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

可以通过dig命令来查询 例如解析www.baidu.com

-> 查找`.`下一级的域名查找服务器

-> 查看`com.`下一级的域名查找服务器

-> 查找`baidu.com.`下一级的域名查找服务器

-> 查找到www.baidu.com.的域名服务器

```bash
dig +trace www.baidu.com

; <<>> DiG 9.8.3-P1 <<>> +trace www.baidu.com
;; global options: +cmd
.			88586	IN	NS	c.root-servers.net.
.			88586	IN	NS	k.root-servers.net.
.			88586	IN	NS	i.root-servers.net.
.			88586	IN	NS	g.root-servers.net.
.			88586	IN	NS	j.root-servers.net.
.			88586	IN	NS	d.root-servers.net.
.			88586	IN	NS	h.root-servers.net.
.			88586	IN	NS	b.root-servers.net.
.			88586	IN	NS	a.root-servers.net.
.			88586	IN	NS	l.root-servers.net.
.			88586	IN	NS	f.root-servers.net.
.			88586	IN	NS	m.root-servers.net.
.			88586	IN	NS	e.root-servers.net.
;; Received 508 bytes from 192.168.8.24#53(192.168.8.24) in 32 ms

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
;; Received 494 bytes from 192.5.5.241#53(192.5.5.241) in 42 ms

baidu.com.		172800	IN	NS	dns.baidu.com.
baidu.com.		172800	IN	NS	ns2.baidu.com.
baidu.com.		172800	IN	NS	ns3.baidu.com.
baidu.com.		172800	IN	NS	ns4.baidu.com.
baidu.com.		172800	IN	NS	ns7.baidu.com.
;; Received 201 bytes from 192.43.172.30#53(192.43.172.30) in 326 ms

www.baidu.com.		1200	IN	CNAME	www.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns4.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns1.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns2.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns3.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns5.a.shifen.com.
;; Received 228 bytes from 220.181.38.10#53(220.181.38.10) in 40 ms
```

这里可能会有几个疑问

> 设置CNAME的作用 

一般普通点的解析可能是这样的

```shell
dig +trace www.404mzk.com
...
...
;; Received 492 bytes from 193.0.14.129#53(193.0.14.129) in 216 ms

404mzk.com.		172800	IN	NS	dns9.hichina.com.
404mzk.com.		172800	IN	NS	dns10.hichina.com.
;; Received 335 bytes from 192.35.51.30#53(192.35.51.30) in 275 ms

www.404mzk.com.		600	IN	A	120.24.37.206
;; Received 48 bytes from 140.205.41.25#53(140.205.41.25) in 32 ms
```

直接拿到IP. 但是为了更好配置比较灵活都会做个CNAME 例如

```shell
dig +trace www.baidu.com
...
...
www.baidu.com.		1200	IN	CNAME	www.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns4.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns1.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns2.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns3.a.shifen.com.
a.shifen.com.		1200	IN	NS	ns5.a.shifen.com.
;; Received 228 bytes from 220.181.38.10#53(220.181.38.10) in 40 ms
```

好处 

*. 批量更改IP方便: 同一IP同时指向一个域名, 例如 A->1.1.1.1 B->1.1.1.1 
    如果此时1.1.1.1的机器发生变了变化, 那么A和B的解析都要变, 如果A CNAME C , B CNAME C
    的话 只要改C的IP即可
    
*. 设置CDN厂商IP: 做CDN 一般域名上CDN 会把自己的域名配置成CDN商的分配的域名 方便CDN做智能DNS 

> 为什么www.baidu.com最后查询还返回了a.shifen.com.的NS?

因为方便CNAME www.a.shi.fen.com去取IP

> 如何拿到根域名服务器的查询地址

例如上面的  

`.			175147	IN	NS	f.root-servers.net.`

DNS服务器要怎么去找`f.root-servers.net.`的IP呢

这些根域的IP 是直接写在DNS服务器中

可以通过`dig ns .` 查看部分根服务器的IP

```shell
> dig ns .

; <<>> DiG 9.8.3-P1 <<>> ns .
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 64857
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 4

;; QUESTION SECTION:
;.				IN	NS

;; ANSWER SECTION:
.			105234	IN	NS	l.root-servers.net.
.			105234	IN	NS	a.root-servers.net.
.			105234	IN	NS	h.root-servers.net.
.			105234	IN	NS	b.root-servers.net.
.			105234	IN	NS	g.root-servers.net.
.			105234	IN	NS	i.root-servers.net.
.			105234	IN	NS	e.root-servers.net.
.			105234	IN	NS	d.root-servers.net.
.			105234	IN	NS	c.root-servers.net.
.			105234	IN	NS	k.root-servers.net.
.			105234	IN	NS	j.root-servers.net.
.			105234	IN	NS	m.root-servers.net.
.			105234	IN	NS	f.root-servers.net.

;; ADDITIONAL SECTION:
a.root-servers.net.	604595	IN	A	198.41.0.4
h.root-servers.net.	604712	IN	A	198.97.190.53
k.root-servers.net.	604712	IN	A	193.0.14.129
f.root-servers.net.	604619	IN	A	192.5.5.241

;; Query time: 27 msec
;; SERVER: 202.96.128.86#53(202.96.128.86)
;; WHEN: Tue Jul 17 00:22:58 2018
;; MSG SIZE  rcvd: 292

```

> 为什么根服务器只有13个IP?

受限于UDP包大小(512) 只能容下13组响应地址

在 Internet 数据传输中，UDP 数据长度控制在 576 字节（Internet 标准 MTU 值），

而在许多 UDP 应用程序设计中数据包被限制成 512 字节或更小。这样可以防止数据包的丢失。

具体的计算细节后面再研究 

> 为什么有些CDN厂商最后返回了多余的NS?

例如

```shell
dig +trace nxnop015.flxdns.com.

...
;; Received 497 bytes from 192.203.230.10#53(192.203.230.10) in 258 ms

flxdns.com.		172800	IN	NS	dns1.flxdns.com.
flxdns.com.		172800	IN	NS	dns2.flxdns.com.
flxdns.com.		172800	IN	NS	dns4.flxdns.com.
flxdns.com.		172800	IN	NS	dns5.flxdns.com.
flxdns.com.		172800	IN	NS	dns6.flxdns.com.
flxdns.com.		172800	IN	NS	h102.dlgslb.cn.
;; Received 240 bytes from 192.12.94.30#53(192.12.94.30) in 303 ms

nxnop015.flxdns.com.	120	IN	A	183.232.201.64
nxnop015.flxdns.com.	120	IN	A	183.232.201.65
nxnop015.flxdns.com.	120	IN	A	183.232.201.88
nxnop015.flxdns.com.	120	IN	A	183.232.201.89
nxnop015.flxdns.com.	120	IN	A	183.240.6.94
nxnop015.flxdns.com.	120	IN	A	183.240.6.95
flxdns.com.		43200	IN	NS	dns1.flxdns.com.
flxdns.com.		43200	IN	NS	dns2.flxdns.com.
flxdns.com.		43200	IN	NS	dns4.flxdns.com.
flxdns.com.		43200	IN	NS	dns5.flxdns.com.
flxdns.com.		43200	IN	NS	dns6.flxdns.com.
flxdns.com.		43200	IN	NS	h102.dlgslb.cn.
;; Received 400 bytes from 122.11.50.214#53(122.11.50.214) in 39 ms

```

在查flxdns.com.的时候已经得到了NS

为什么最后查nxnop015.flxdns.com.的时候还返回了flxdns.com.的ns?

后续再研究.暂时推测是加热DNS服务器缓存?

参考: 

1. https://jaminzhang.github.io/dns/The-Reason-of-There-Is-Only-13-DNS-Root-Servers/
2. https://www.zhihu.com/question/22587247

# DNSPod

TTL的含义

https://blog.csdn.net/zhanghaiyang9999/article/details/39505861

traceroute

# NS记录

# 浏览器加载资源的顺序

# chrome devtools 的network

https://developers.google.com/web/tools/chrome-devtools/network-performance/reference

# 参考链接

1. DNS解析: https://blog.csdn.net/crazw/article/details/8986504
2. DNS解析: http://www.ruanyifeng.com/blog/2016/06/dns.html