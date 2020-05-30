# CDN原理解析

# 简介

CDN解决的问题: 给用户找到(物理距离, 机器负载情况) 给你返回最适合的机器的IP

# 术语简介

### 域名解析

- A记录: www.xxx.com 10.5.2.101, 域名直接跟IP对应关系
- NS记录: xxx.com 10.2.3.5 表示DNS服务器在解析xxx.com子域名的时候 找10.2.3.5这个DNS解析服务器解析
- CNAME记录: www.xxx.com CNAME www.xxx.com.cnd.com DNS服务器找www.xunlei.com的IP的时候, 要去问www.www.com.cnd.com这个服务器拿

### GSLB

`全局负载均衡系统`-GSLB

一般是指CNAME的服务器中拥有的功能

根据(物理距离, 机器负载情况) 给你返回最适合的机器IP

这个IP的机器后面大概率是SLB

### SLB

负载均衡(Server Load Balancing，SLB)

SLB后面会有N台缓存服务器

SLB会根据(机器负载情况)去访问缓存服务器, 并把缓存服务器返回的内容返回给服务器

# 大体流程

假设访问 `zzzz.xxx.com/a.js`

客户端->本地DNS->根DNS->顶级DNS->权威DNS->GSLB

这里GSLB会给客户端一个IP, 假设这个IP是一个SLB

客户端->SLB->缓存服务器

本地DNS就是运营商

根DNS解析com.的DNS服务器

顶级DNS是解析xxx.com的DNS服务器

权威DNS是指解析www.xxx.com的DNS服务器

# 通过命令行查看 域名解析情况

### dig

```
$ dig www.qq.com

; <<>> DiG 9.10.6 <<>> www.qq.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16411
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 3, ADDITIONAL: 12

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;www.qq.com.			IN	A

;; ANSWER SECTION:
www.qq.com.		193	IN	CNAME	public-v6.sparta.mig.tencent-cloud.net.
public-v6.sparta.mig.tencent-cloud.net.	97 IN A	113.96.232.215
public-v6.sparta.mig.tencent-cloud.net.	97 IN A	14.18.175.154

;; AUTHORITY SECTION:
tencent-cloud.net.	14705	IN	NS	ns-open3.qq.com.
tencent-cloud.net.	14705	IN	NS	ns-open1.qq.com.
tencent-cloud.net.	14705	IN	NS	ns-open2.qq.com.

;; ADDITIONAL SECTION:
ns-open1.qq.com.	1866	IN	A	59.36.132.139
ns-open1.qq.com.	1866	IN	A	120.204.1.100
ns-open1.qq.com.	1866	IN	A	203.205.236.176
ns-open2.qq.com.	2124	IN	A	182.254.48.197
ns-open2.qq.com.	2124	IN	A	182.254.116.5
ns-open2.qq.com.	2124	IN	A	58.251.121.188
ns-open3.qq.com.	172	IN	A	180.163.22.39
ns-open3.qq.com.	172	IN	A	203.205.220.25
ns-open3.qq.com.	172	IN	A	58.246.221.60
ns-open3.qq.com.	172	IN	A	121.51.167.100
ns-open3.qq.com.	172	IN	A	125.39.46.36
```

主要看` ANSWER SECTION`这个部分

就是先找到CNAME, 然后再找到一个具体的A解析的IP

### host

```
$ host www.qq.com
www.qq.com is an alias for public-v6.sparta.mig.tencent-cloud.net.
public-v6.sparta.mig.tencent-cloud.net has address 113.96.232.215
public-v6.sparta.mig.tencent-cloud.net has address 14.18.175.154
public-v6.sparta.mig.tencent-cloud.net has IPv6 address 240e:ff:f101:10::15f
public-v6.sparta.mig.tencent-cloud.net has IPv6 address 240e:ff:f101:10::14d
```

# 参考链接

- cdn原理: https://juejin.im/post/5d105e1af265da1b71530095
- 阿里云cdn原理讲解: https://help.aliyun.com/document_detail/122172.html?spm=a2c4g.11186623.6.552.545f5d17EtCyzb