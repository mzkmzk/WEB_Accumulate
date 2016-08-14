# 设置慢查询

# 1. 用途

偶尔服务器502,我的程序到底哪里性能低了?如何跟踪,设置慢查询是个不错的选择

# 2. 设置

在php-fpm.conf中设置

```php
request_slowlog_timeout = 5s
slowlog = /usr/local/php/log/php-fpm.log.slow
```