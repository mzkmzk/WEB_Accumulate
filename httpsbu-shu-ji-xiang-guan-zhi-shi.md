# HTTPS部署及相关知识

# 为什么要用HTTPS

> 防止运营商劫持

运营商不止简单的在页面上加iframe广告

还会串改我们的JS

导致JS加载顺序错乱

严重的话会因为引发JS错误导致整个页面无法交互

> 加密传输内容

HTTP的传输内容都是明文的

这就相当于谁能控制路由器, 谁就能获得并修改你所有的网络传输数据

# HTTPS是如何加密数据的

传统的http传输方式是 第四步的TCP到第七层的HTTP层之间是明文传输的

需要在这加一层负责传输加解密的层(SSL/TLS)

HTTP收到的时候都是明文, 对原先逻辑不影响

更加具体的传输过程可见https://tools.ietf.org/html/rfc5246

> 加密是在OSI 7层模型的哪几层

先看下7层的分工

```bash
物理层: 为数据链路层提供物理连接，在其上串行传送比特流，即所传送数据的单位是比特。此外，该层中还具有确定连接设备的电气特性和物理特性等功能。 

数据链路层: 负责在网络节点间的线路上通过检测、流量控制和重发等手段，无差错地传送以帧为单位的数据。为做到这一点，在每一帧中必须同时带有同步、地址、差错控制及流量控制等控制信息。 

网络层: 为了将数据分组从源（源端系统）送到目的地（目标端系统），网络层的任务就是选择合适的路由和交换节点，使源的传输层传下来的分组信息能够正确无误地按照地址找到目的地，并交付给相应的传输层，即完成网络的寻址功能。 

传输层: 传输层是高低层之间衔接的接口层。数据传输的单位是报文，当报文较长时将它分割成若干分组,然后交给网络层进行传输。传输层是计算机网络协议分层中的最关键一层，该层以上各层将不再管理信息传输问题。 

会话层: 该层对传输的报文提供同步管理服务。在两个不同系统的互相通信的应用进程之间建立、组织和协调交互。例如，确定是双工还是半双工工作。 

表示层: 该层的主要任务是把所传送的数据的抽象语法变换为传送语法，即把不同计算机内部的不同表示形式转换成网络通信中的标准表示形式。此外，对传送的数据加密（或解密）、正文压缩（或还原）也是表示层的任务。 

应用层: 该层直接面向用户，是OSI中的最高层。它的主要任务是为用户提供应用的接口，即提供不同计算机间的文件传送、访问与管理，电子邮件的内容处理，不同计算机通过网络交互访问的虚拟终端功能等。
```

参考: https://blog.csdn.net/Better2326/article/details/48371241

所以SSL/TLC 是工作在 会话层和应用层的

## TLS基本流程

1. 客户端向服务器拿公钥并验证公钥
2. 双方协商生成"对话密钥"
3. 双方采用"对话密钥"进行加密通信

前两部叫做"握手阶段"

## "握手阶段"详细过程

> 客户端发送请求ClientHello

客户端向服务器提供一下信息

1. 支持的协议版本，比如TLS 1.2版
2. 一个客户端生成的随机数, 稍后用于生成"对话密钥"

wiresharek截图数据

![clienthello](/assets/QQ20171220-230822.png)

> 服务器回应ServerHello

1. 确认使用的加密通信协议版本，比如TLS 1.0版本。如果浏览器与服务器支持的版本不一致，服务器关闭加密通信。
2. 一个服务器生成的随机数，稍后用于生成"对话密钥"。
3. 确认使用的加密方法，比如RSA公钥加密
4. 服务器证书。

![ServerHello](/assets/QQ20171221-143247.png)

![ServerHello](/assets/QQ20171220-232703.png)

> 客户端回应

客户端验证证书的正确性后, 确定被信任或使用者同意使用不安全证书, 则发送以下内容

1. 一个随机数。该随机数用服务器公钥加密，防止被窃听。
2. 编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送。
3. 客户端握手结束通知，表示客户端的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供服务器校验。

![客户端回应](/assets/QQ20171220-233351.png)

> 服务器回应

1. 用私钥解密客户端最后给的随机数, 得到对称性密钥
2. 编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送。
3. 服务器握手结束通知，表示服务器的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供客户端校验。

最后也加上整体的wireshark抓包记录

![服务器回应](/assets/QQ20171220-233606.png)

整体交互流程
```
Client                                               Server

ClientHello                  -------->
                                                ServerHello
                                               Certificate*
                                         ServerKeyExchange*
                                        CertificateRequest*
                             <--------      ServerHelloDone
Certificate*
ClientKeyExchange
CertificateVerify*
[ChangeCipherSpec]
Finished                     -------->
                                         [ChangeCipherSpec]
                             <--------             Finished
Application Data             <------->     Application Data
```

![四次握手图](/assets/bg2014092003.png)

由于在四次握手中是明文的

所以整个阶段的关键只在于第三个客户端的对话密钥能否被解密

还有其他优化握手速度的解决方案

1. CDN帮忙完成(除了解密对话密钥的步骤)握手
2. 运用DH算法

具体可参看<http://www.ruanyifeng.com/blog/2014/09/illustration-ssl.html>



## SSL和TLS的关联

```
1994年，NetScape公司设计了SSL协议（Secure Sockets Layer）的1.0版，但是未发布。

1995年，NetScape公司发布SSL 2.0版，很快发现有严重漏洞。

1996年，SSL 3.0版问世，得到大规模应用。

1999年，互联网标准化组织ISOC接替NetScape公司，发布了SSL的升级版TLS 1.0版。

2006年和2008年，TLS进行了两次升级，分别为TLS 1.1版和TLS 1.2版。最新的变动是2011年TLS 1.2的修订版。//https://tools.ietf.org/html/rfc6176
```

# 服务器证书

估计HTTPS大家都很希望在浏览器中看到类似Gihutb Inc的小绿标

这些有公司名的小绿标和普通的小绿标差别在哪

![服务器证书](/assets/QQ20171226-231907.png)

其加密效果都是一样的 但是面向的对象不一样

1. DV（Domain Validation），面向个体用户，安全体系相对较弱，验证方式就是向 whois 信息中的邮箱发送邮件，按照邮件内容进行验证即可通过；
2. OV（Organization Validation），面向企业用户，证书在 DV 证书验证的基础上，还需要公司的授权，CA 通过拨打信息库中公司的电话来确认；
3. EV（Extended Validation），打开 Github 的网页，你会看到 URL 地址栏展示了注册公司的信息，这会让用户产生更大的信任，这类证书的申请除了以上两个确认外，还需要公司提供金融机构的开户许可证，要求十分严格。


引自`http://www.barretlee.com/blog/2016/04/24/detail-about-ca-and-certs/`



# 常用文件类型

> `.key` 

一串密钥 相当于密码

> `.crt`

证书文件
> `.csr`

证书请求文件, 用于发送给CA中心等待签发

> `.pem(Privacy Enhanced Mail)`

存放各种信息, 是openssl默认采用的信息存放方式

其内容只要包括 

1. 内容类型: ---BEGIN XXX--- ... ---END XXX ---来表示存放的是什么信息内容
2. 头信息: 表名数据是如何被处理后存放的,openssl中用的最多的是加密信息, 比如加密算法及初始化向量iv
3. 信息体: BASE64编码数据. 可以保护私钥(RSA和DSA)、公钥(RSA和DSA私钥)和(x509)证书, 它存储用Base64编码的DER格式数据, 用ascii报头包围, 因此适合系统之间的文本传输模式

例如存储证书的pem.crt一般为

```
—–BEGIN CERTIFICATE—–
MIICJjCCAdCgAwIBAgIBITANBgkqhkiG9w0BAQQFADCBqTELMAkGA1UEBhMCVVMx
………
1p8h5vkHVbMu1frD1UgGnPlOO/K7Ig/KrsU=
—–END CERTIFICATE—–
```

例如存储私钥的pem.key一般为

```
—–BEGIN RSA PRIVATE KEY—–
MIICJjCCAdCgAwIBAgIBITANBgkqhkiG9w0BAQQFADCBqTELMAkGA1UEBhMCVVMx
………
1p8h5vkHVbMu1frD1UgGnPlOO/K7Ig/KrsU=
—–END RSA PRIVATE KEY—–
```

例如存储证书请求文件的pem.csr一般为

```
—–BEGIN CERTIFICATE REQUEST—–
MIICJjCCAdCgAwIBAgIBITANBgkqhkiG9w0BAQQFADCBqTELMAkGA1UEBhMCVVMx
………
1p8h5vkHVbMu1frD1UgGnPlOO/K7Ig/KrsU=
—–END CERTIFICATE REQUEST—–
```




# 生成根证书理论逻辑

这里举例的是 

1. 生成根证书
2. 根证书签发中级证书
3. 中级证书签发用户证书

然则具体的逻辑是

1. 随机生成密钥`rootca.key`
2. 根据`rootca.key`生成证书`rootca.crt`
3. 配置根证书的配置文件ca.conf(用于给别人签发证书时, 别人需要知道根证书的信息)
4. 随机生成密钥-`intermediate1.key`
5. 根据intermediate1.key生成待确认信息文件`intermediate1.csr`
6. 根据ca.conf、intermediate1.csr 生成中级CA证书`intermediate1.crt`
7. 配置中级证书的配置文件`intermediate1.conf`
8. 随机生成密钥`enduser-example.com.key`
9. 根据`enduser-example.com.key`生成待确认信息文件`enduser-example.com.csr`
10. 用`intermediate1.conf`和`enduser-example.com.csr`生成用户最终证书`enduser-example.com.crt`
11. 将enduser-example.com.crt、intermediate1.crt和rootca.crt合并在一起成为最终的用户证书`enduser-example.com.chain.crt`
12. 配置`enduser-example.com.chain.crt`和`enduser-example.com.key`在nginx

具体可参考: https://linux.cn/article-6498-1.html

tips:

1. 配置文件是存储着签发着的私钥密钥路径及各种信息的文件
2. crl: 证书吊销列表 (Certification Revocation List) 是一种包含撤销的证书列表的签名数据结构


# Mac OS下签发多域名证书

## 生成根证书

打开`/Applications/Utilities/钥匙串访问`

菜单中 钥匙串访问属性->证书助理->创建证书

以下为需要注意的步骤, 其他步骤随便填写即可

需要注意的点是要勾选让我覆盖这些默认值

![mac os签发证书-1](/assets/step-1.png)

只勾选证书签名和CRL签名

![mac os签发证书-2](/assets/step-5.png)

证书需作为签发机构

![mac os签发证书-3](/assets/step-7.png)

其余的基本下一步即可

生成完证书后 要完全信任

![信任根证书](/assets/信任.png)

## 生成多域名证书

> 导出自签名证书

一定要导出时证书和密钥同时导出, 命名为ca.p12,会提示设置密码, 不设置也可以, 但是之后要删除ca.p12 以防信息泄露

![导出根证书](/assets/导出.png)

> 创建多域名配置文件

命名为san.cnf跟ca.p12放于同一目录

DNS.* 中可填写任意多域名 

```
[ req ]
default_bits       = 2048
distinguished_name = req_distinguished_name
req_extensions     = req_ext
[ req_distinguished_name ]
countryName                 = Country Name (2 letter code)
stateOrProvinceName         = State or Province Name (full name)
localityName               = Locality Name (eg, city)
organizationName           = Organization Name (eg, company)
commonName                 = Common Name (e.g. server FQDN or YOUR name)
[ req_ext ]
subjectAltName = @alt_names
[alt_names]
DNS.1   = *.example.com
DNS.2   = example.com
DNS.3   = localhost
```

然后在ca.p12和san.cnf的目录中输入

```
openssl pkcs12 -clcerts -nokeys -out ca-cert.pem -in ca.p12  #导出公钥
openssl pkcs12 -nocerts -nodes -out ca-key.pem -in ca.p12 #导出私钥
```

中间户提示输入导出ca.p12时设置的密码, 如果没有设置 回车即可

```
openssl req -out user-cert.csr -newkey rsa:2048 -nodes -keyout private.key -config san.cnf #创建多域名证书请求
openssl x509 -req -in user-cert.csr -CAcreateserial -CA ca-cert.pem -CAkey ca-key.pem -out cert.pem -days 500 -sha256 -extfile san.cnf -extensions req_ext #签发证书
```

执行过程会提示信息, COmmon Name写成要申请的主要域名 例如example.com之类的

其余不知道就回车跳过

最后生成的

cert.pem为证书和private.key为证书私钥

配置nginx

```
listen  80;
listen  443 ssl;
server_name  example.com;
ssl_certificate      pki/cert.pem;
ssl_certificate_key  pki/private.key;
```

# 各系统信任证书设置

在内网环境中, 我们经常需要使用到 自己伪造的证书

那么我们就要让我们的系统信任它, 而防止HTTPS请求被浏览器拦截

> WINDOWS

1. 搜索->管理计算机证书
2. 受信任的根证书颁发机构->证书->所有任务->导入
3. 选择根证书的公钥.crt

> Mac

1. 钥匙串访问
2. 选择系统根证书
3. 菜单栏->文件->导入项目
4. 选择根证书的公钥.crt

> 手机端

一般内网环境 如果是手机通过访问Fiddle设置的代理 从而访问内网环境的

满足以下条件手机信任伪造证书

1. PC开启Fiddle的HTTPS代理
2. PC信任伪造证书
3. 手机连接Fiddle并信任Fiddle证书

关于手机如何信任Fiddle证书, 

1. Fiddle打开HTTPS设置
2. 手机内置浏览器中访问Fiddle的ip:代理端口
3. 进入的页面 有个按钮可以下载证书 信任即可

Fillder打开HTTPS设置(Tools->Options->HTTPS)

![Fillder打开HTTPS设置](/assets/QQ20171227-141023.png)

> Firefox

Firefox和chrome IE等不一样

chrome ie的信任证书是跟操作系统信任的证书一致的

而firefox信任的正式是走自己的, 所以要单独信任证书

1. 选项->隐私与安全->查看证书
2. 导入->选择证书

# 部署HTTPS后 容易遇到的问题

> 检查证书链是否完整

如果缺少中间证书

1. 部分浏览器能正常显示, 但严重影响TLS握手性能
2. 部分浏览器直接爆证书错误 , 页面无法展示

> 检查系统时间

如果用户电脑时间不对，也会导致浏览器提示证书有问题，这时浏览器一般都会有明确的提示，例如 Chrome 的 ERR_CERT_DATE_INVALID。

> 第三方资源

页面中难免有时引用第三方资源, 而第三方却不支持HTTPS

1. 如果是接口: 可通过nginx代理
2. 如果是资源: 可以通过nginx代理或把资源存放在自己的https域名里

需要注意的是 

1. 代理会造成第三方通过REMOTE_ADDR拿到的是代理IP, 很可能导致这个IP被限制请求频率或被封
2. 代理只能拿到自己域名下的cookie, 需要从其他域获取Cookie的第三方接口被代理可能不能正常工作
3. 代理对Referrer、被代理的URL都需要做好白名单机制

> Referrer

目前大多数浏览器在https的页面点击跳转到HTTP网站时, 并不会在请求头带上Referrer

可以通过在html里增加这个meta

`<meta name="referrer" content="always" />`



# HTTPS的页面必须所有资源都是HTTPS的吗

最好是, 但是有些http资源在https页面 也能显示出来 

首先说下Mixed Content的概念

HTTPS 网页中加载的 HTTP 资源被称之为 Mixed Content（混合内容），不同浏览器对 Mixed Content 有不一样的处理规则。

> 早期的IE

发现Mixed Content的时候, 会弹出`是否只查看安全传送的网页内容? `静态对话框 

1. 用户选择是则不加载所有Mixed Content
2. 用户选择否则加载所有Mixed Content

> 比较新的IE

静态对话框改为底部的提示条, 而且默认加载图片类的Mixed Content

> 现代浏览器

都遵守W3C的Mixed Content规范(https://www.w3.org/TR/mixed-content/)

将Mixed Content分为

1. Optionally-blockable: `<img>`, `<video>`, `<audio>`, `<source>`, Prefetched资源
2. blockable: 不在Optionally-blockable范围内的



# HSTS

以为上了HTTPS就相安无事了吗

运营商就无法劫持了吗..看一下下图

![HTTPS劫持](/assets/20151004_f70b86fd.jpg)

一般用户输入xxx.com时, 并不会前面写上https://xxx.com

而且输入xxx.com到服务器之后 ,服务器再重定向到https://xxx.com

但如果在这之前被劫持了, 就是上诉情况了

我们可以通过nginx启动HSTS

```
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
```

这样只要用户访问了一次https://xxx.com 浏览器就会在max-age里 

如果用户再访问xxx.com 浏览器会访问307直接去https://xxx.com

好处在于

1. 节省一次 302/301 的跳转
2. 能在有效期内防止中间人劫持 





# 常用检查工具

> 查看证书链接

`openssl s_client -connect mac-act-vip-ssl.?.com:443`

```
Certificate chain
 0 s:/CN=Thunder-Vip/ST=Some-State/C=AU/O=Internet Widgits Pty Ltd
   i:/CN=Thunder-Middle/ST=Some-State/C=AU/O=Internet Widgits Pty Ltd
 1 s:/CN=Thunder-Middle/ST=Some-State/C=AU/O=Internet Widgits Pty Ltd
   i:/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd/CN=Thunder-Root
 2 s:/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd/CN=Thunder-Root
   i:/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd/CN=Thunder-Root
---
```
s代表本证书

i代表s中的证书是谁发的

> 检查SSL情况网址

https://www.ssllabs.com/ssltest/analyze.html

> 补全证书中间链工具

1. 命令工具: https://github.com/spatie/ssl-certificate-chain-resolver
2. 在线网址: https://certificatechain.io/


# 注意点

> firefox单独管理信任证书

firefox信任的证书在mac下 不跟系统走, 而是要firefox自己有一套自己信任根证书, 在系统添加完信任证书后, 要在firefox再添加一遍 

> 整合中间证书

一般是根证书->中间证书->服务器证书

nginx中的crt需要把三个证书合并在一起 顺序是

```
服务器证书
中间机构证书
根证书
```

顺序错了 nginx会报错 

```
SSL_CTX_use_PrivateKey_file(" ... /www.example.com.key") failed
   (SSL: error:0B080074:x509 certificate routines:
    X509_check_private_key:key values mismatch)
```

> 管理员配置错证书

当管理员配置错中间证书(例如缺少中间证书链), 但假如管理员之前配置正确过

浏览器信任过这个证书, 会在管理员电脑中正常

但是其他用户缺不正常

因为浏览器验证颁发证书机构时 有缓存策略

```
A、浏览器自安装以来，从未见过这个i。那么验证会失败。
B、浏览器以前见过、并且验证过i，那么验证会成功。
```





# 参考资料

1. mac生成根证书及生成多域名证书: https://www.zhoumingzhi.com/2016/11/15/macos%E4%B8%8B%E7%AD%BE%E5%8F%91%E5%A4%9A%E5%9F%9F%E5%90%8D%E8%AF%81%E4%B9%A6/, 但是在firefox会有 SEC_ERROR_INADEQUATE_CERT_TYPE 错误 其他浏览器正常
2. 详细的CA类别说明和自建根证书: http://www.barretlee.com/blog/2016/04/24/detail-about-ca-and-certs/
3. nginx使用中间证书: http://blog.csdn.net/gudufeiyang/article/details/58603402
4. openssl数字证书常见格式与协议介绍: http://www.huangxiaobai.com/archives/1739
5. 命令安装多级证书: https://linux.cn/article-6498-1.html
6. 英文版命令安装多级证书: https://raymii.org/s/tutorials/OpenSSL_command_line_Root_and_Intermediate_CA_including_OCSP_CRL%20and_revocation.html
7. openssl配置文件讲解: https://www.phildev.net/ssl/opensslconf.html
8. 清楚详细的命令行签发证书, 但是有个命令报错: http://blog.csdn.net/howeverpf/article/details/21622545
9. 阮一峰加密过程: http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html#comment-349225#comment-text
10. tls相关rfc: https://tools.ietf.org/html/rfc5246
11. 浏览器HTTPS错误码解释: https://hg.mozilla.org/l10n-central/zh-CN/file/default/security/manager/chrome/pipnss/nsserrors.properties
12. HSTS阅读: http://www.barretlee.com/blog/2015/10/22/hsts-intro/
13. 证书相关说明: http://www.barretlee.com/blog/2016/04/24/detail-about-ca-and-certs/
14. Fiddle模拟HTTPS原理: https://www.jianshu.com/p/54dd21c50f21
15. ququ的HTTPS系列文章1: https://imququ.com/post/sth-about-switch-to-https.html
17. ququ的HTTPS系列文章3: https://imququ.com/post/sth-about-switch-to-https-3.html
