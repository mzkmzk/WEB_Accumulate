# HTTPS部署及相关知识

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
chrome和safari等浏览器都可通过

但是firefox还是会提示证书不可用 应该是直接由根证书签署到网站证书 缺少中间证书的原因 暂未解决 后面再深究


# 参考资料

1. mac生成根证书及生成多域名证书: https://www.zhoumingzhi.com/2016/11/15/macos%E4%B8%8B%E7%AD%BE%E5%8F%91%E5%A4%9A%E5%9F%9F%E5%90%8D%E8%AF%81%E4%B9%A6/
2. 详细的CA类别说明和自建根证书: http://www.barretlee.com/blog/2016/04/24/detail-about-ca-and-certs/
