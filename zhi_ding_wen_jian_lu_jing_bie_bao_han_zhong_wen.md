# 指定文件路径别包含中文


# 问题

在`apache2/extra/httpd-vhosts.conf`中

如果有DocumentRoot指定了中文路径,现在尝试了以下几种情况

1. 该DcouemntRoot非最后一个域名,则仅写在它下面的一个域名无法解析
2. 该DocumentRoot是最后一个域名,则仅自己无法解析

看看apache的error_log就清楚了


```shell
[Sat Dec 03 16:27:19.300954 2016] [:error] [pid 65637] [client 127.0.0.1:63484] mod_hfs_apple: Mis-cased URI or unacceptable Unicode in URI: /Users/maizhikun/Project/\xe8\xbf\x85\xe9\x9b\xb7/\xe5\xbf\xab\xe9\xb8\x9f\xe6\x8f\x92\xe4\xbb\xb6/code/speed-mini-xl9/index.html, wants: /Users/maizhikun/Project/thunder/LiXianSpace/code/
[Sat Dec 03 16:27:19.368695 2016] [:error] [pid 65637] [client 127.0.0.1:63484] mod_hfs_apple: Mis-cased URI or unacceptable Unicode in URI: /Users/maizhikun/Project/\xe8\xbf\x85\xe9\x9b\xb7/\xe5\xbf\xab\xe9\xb8\x9f\xe6\x8f\x92\xe4\xbb\xb6/code/favicon.ico, wants: /Users/maizhikun/Project/maizhikun/LiXianSpace/code/, referer: http://k.maizhikun.com/speed-mini-xl9/index.html?dnbandwidth=2&maxdnbandwidth=50&usertype=6
```