# 用Let's-Encrypt部署HTTPS

# 申请证书

> 创建账号私钥

`openssl genrsa 4096 > 404mzk-com-account.key`

> 创建域名私钥

`openssl genrsa 4096 > 404mzk-com-private.key`

> 创建申请认证的.csr文件

`openssl req -new -sha256 -key 404mzk-com-private.key -subj "/" -reqexts SAN -config <(cat /etc/ssl/openssl.cnf <(printf "[SAN]\nsubjectAltName=DNS:www.404mzk.com,404mzk.com")) > 404mzk-com.csr`

这里需要注意的是openssl.cnf的位置各个系统不太一致

1. mac: /System/Library/OpenSSL/openssl.cnf
2. ubuntu: /etc/ssl/openssl.cnf

需要生成多个域名则在DNS中写多个域名

tips: 2018年1月Let's-Encrypt正式支持泛域名

但是由于本文也是在2018月1月所写, 所以还没尝试泛域名的操作, 后面尝试了会在本文补充

> Let's-Encrypt验证域名所有权

通常CA验证方式是往admin@yousite.com发验证邮件

而Let's-Encrypt是在你的服务器生成一个随机验证文件

先设置nginx的访问配置

```
server {
    server_name www.yoursite.com yoursite.com;

    # Let'Encrypt的验证访问路径
    location ^~ /.well-known/acme-challenge/ {
        alias /home/xxx/www/challenges/;
        try_files $uri =404;
    }
    
    # 这段是强制访问https的限制 和验证证书无关
    location / {
        rewrite ^/(.*)$ https://yoursite.com/$1 permanent;
    }
}
```

执行校验命令

```
wget https://raw.githubusercontent.com/diafygi/acme-tiny/master/acme_tiny.py
python acme_tiny.py --account-key ./404mzk-com-account.key --csr ./404mzk-com.csr --acme-dir /home/xxx/www/challenges/ > ./404mzk-signed.crt
```

注意

`--acme-dir`和nginx中设置的`^~ /.well-known/acme-challenge/ alias`访问路径必须指向一致

因为Let's-Encrypt的校验原来是

1. 往通过acme_tiny.py命令向`--acme-dir`指定的路径生成一个随机文件
2. Let's-Encrypt访问生成csr时指定的域名中的`/.well-known/acme-challenge/`路径访问随机文件.
3. 校验成功即在本地生成crt证书文件

> 补全中间链

上述命令得到的是我们的域名(404mzk.com)的证书文件

但是还缺少Let's-Encrypt这个中间证书文件和根证书文件

所以需要串在一块

```
wget -O - https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem > intermediate.pem
wget -O - https://letsencrypt.org/certs/isrgrootx1.pem > root.pem

cat 404mzk-signed.crt intermediate.pem root.pem> chained.pem

```

> 配置nginx证书

```bash
ssl_certificate     ~/www/ssl/chained.pem;
ssl_certificate_key ~/www/ssl/404mzk.key;
```

> 配置自动更新

Let's-Encrypt颁发证书的有效性只有3个月

所以建议用个脚本, 隔段时间自己自动去更新

```bash
#!/bin/bash

cd /var/www/ssl/k-inner-report-...-com
python ./acme_tiny.py --account-key 404mzk-com-account.key --csr 404mzk-com.csr --acme-dir ./challenges/ > 404mzk-com.crt || exit
wget -O - https://letsencrypt.org/certs/lets-encrypt-x3-cross-signed.pem > intermediate.pem
wget -O - https://letsencrypt.org/certs/isrgrootx1.pem > root.pem
cat 404mzk-com.crt intermediate.pem root.pem > chained.pem
/usr/sbin/nginx -s reload
```

加入定时脚本

```
> vim /etc/crontab
> 0 0 1 * * root /home/xxx/shell/renew_cert.sh > /var/www/ssl/xxx-com/renew_cert.log 2>&1

```


# 参考文章

1. https://imququ.com/post/letsencrypt-certificate.html
