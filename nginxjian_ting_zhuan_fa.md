# Nginx监听转发

# 1. 需求

是本地的node`locatiot: 4000 `

但阿里那边不允许针对端口配置域名

只好通过nginx转发

# 2. 实现

```bash
  upstream nodejs__upstream {
       server 127.0.0.1:4000;
       keepalive 64;
   }
   server {
       listen 80;
       server_name front.docs.grouplus.com;
       error_log  /data/nginx/logs/docs-error.log;
       access_log  /data/nginx/logs/docs-access.log main;
      location / {
         proxy_set_header   X-Real-IP            $remote_addr;
         proxy_set_header   X-Forwarded-For      $proxy_add_x_forwarded_for;
          proxy_set_header   Host                 $http_host;
          proxy_set_header   X-NginX-Proxy        true;
          proxy_set_header   Connection "";
          proxy_http_version 1.1;
          proxy_pass         http://nodejs__upstream;
      }
 }
```

# 参考链接

<http://blog.fens.me/nodejs-nginx-log4js/>