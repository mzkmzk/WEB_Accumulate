# 更改301定向

需求: 以前只有.cn域名,现在要兼容.com域名

其实为了SEO,假如baidu.com和baidu.cn域名还有加www和不加www

最好都统一跳到一个url,例如www.baidu.com
这样方便SEO去统计

# Nginx配置

设置配置文件

```shell
  6     server_name  xxx.cn www.com;
  7     if ($host != xxx.cn) {
  8         rewrite ^/(.*)$ http://xxx.cn/$1 permanent;
  9     }
```