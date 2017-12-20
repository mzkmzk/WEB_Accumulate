# HTTPS部署及相关知识

# 为什么要用HTTPS

> 防止运营商劫持

运营商不止简单的在页面上加iframe广告

还会串改我们的JS

导致JS加载顺序错乱

严重的话会因为引发JS错误导致整个页面无法交互

> 加密传输内容

HTTPS的传输内容都是明文的

这就相当于谁能控制路由器, 谁就能获得并修改你所有的网络传输数据

# HTTPS是如何加密数据的

传统的http传输方式是 第四步的TCP到第七层的HTTP层之间是明文传输的

需要在这加一层负责传输加解密的层(SSL/TLS)

HTTP收到的时候都是明文, 对原先逻辑不影响

更加具体的传输过程可见https://tools.ietf.org/html/rfc5246

## TLS基本流程

1. 客户端向服务器拿公钥并验证公钥
2. 双方协商生成"对话密钥"
3. 双方采用"对话密钥"进行加密通信

前两部叫做"握手阶段"

## "握手阶段"详细过程

> 客户端发送请求ClientHello

客户端向服务器提供一下信息

1. 支持的协议版本，比如TLS 1.0版
2. 一个客户端生成的随机数, 稍后用于生成"对话密钥"
3. 支持的加密方法，比如RSA公钥加密
4. 支持的压缩方法

wiresharek截图数据

![clienthello](/assets/QQ20171220-230822.png)

> 服务器回应ServerHello

1. 确认使用的加密通信协议版本，比如TLS 1.0版本。如果浏览器与服务器支持的版本不一致，服务器关闭加密通信。
2. 一个服务器生成的随机数，稍后用于生成"对话密钥"。
3. 确认使用的加密方法，比如RSA公钥加密
4. 服务器证书。

![ServerHello](/assets/QQ20171220-232703.png)

> 客户端回应

1. 一个随机数。该随机数用服务器公钥加密，防止被窃听。
2. 编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送。
3. 客户端握手结束通知，表示客户端的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供服务器校验。

![客户端回应](/assets/QQ20171220-233351.png)

> 服务器回应

1. 编码改变通知，表示随后的信息都将用双方商定的加密方法和密钥发送。
2. 服务器握手结束通知，表示服务器的握手阶段已经结束。这一项同时也是前面发送的所有内容的hash值，用来供客户端校验。

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

## SSL和TLS的关联

```
1994年，NetScape公司设计了SSL协议（Secure Sockets Layer）的1.0版，但是未发布。

1995年，NetScape公司发布SSL 2.0版，很快发现有严重漏洞。

1996年，SSL 3.0版问世，得到大规模应用。

1999年，互联网标准化组织ISOC接替NetScape公司，发布了SSL的升级版TLS 1.0版。

2006年和2008年，TLS进行了两次升级，分别为TLS 1.1版和TLS 1.2版。最新的变动是2011年TLS 1.2的修订版。//https://tools.ietf.org/html/rfc6176
```



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
2. 根证书签发中间证书
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

tips:

1. 配置文件是存储着签发着的私钥密钥路径及各种信息的文件
2. crl: 证书吊销列表 (Certification Revocation List) 是一种包含撤销的证书列表的签名数据结构

看完上面你的疑问可能是

1. 要crl干啥? 第7,8 10 11 15 16都在生成crl




# Mac OS下签发多域名证书

## 生成根证书

打开`/ApplicationsUtilities/钥匙串访问`

菜单中'钥匙串访问'->证书助理->创建证书

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


# HPKP

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

i代表s中的正式是谁发的

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

# 遗留问题

导出xunlei-maizhikun根证书crt 解决 firefox问题 

# 参考资料

1. mac生成根证书及生成多域名证书: https://www.zhoumingzhi.com/2016/11/15/macos%E4%B8%8B%E7%AD%BE%E5%8F%91%E5%A4%9A%E5%9F%9F%E5%90%8D%E8%AF%81%E4%B9%A6/
2. 详细的CA类别说明和自建根证书: http://www.barretlee.com/blog/2016/04/24/detail-about-ca-and-certs/
3. nginx使用中间证书: http://blog.csdn.net/gudufeiyang/article/details/58603402
4. openssl数字证书常见格式与协议介绍: http://www.huangxiaobai.com/archives/1739
5. 命令安装多级证书: https://linux.cn/article-6498-1.html
6. 英文版命令安装多级证书: https://raymii.org/s/tutorials/OpenSSL_command_line_Root_and_Intermediate_CA_including_OCSP_CRL%20and_revocation.html
7. openssl配置文件讲解: https://www.phildev.net/ssl/opensslconf.html
8. 清楚详细的命令行签发证书, 但是有个命令报错: http://blog.csdn.net/howeverpf/article/details/21622545
