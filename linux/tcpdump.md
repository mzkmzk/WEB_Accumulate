# tcpdump

## 实战技巧

### 抓取Get请求url和响应内容

```bash
tcpdump -A -s 0 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
```

[参考](https://stackoverflow.com/questions/4777042/can-i-use-tcpdump-to-get-http-requests-response-header-and-response-body)

### 抓取Post请求url和响应内容

```
tcpdump -X -s 0 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
```

[参考](https://stackoverflow.com/questions/4777042/can-i-use-tcpdump-to-get-http-requests-response-header-and-response-body)

## 参考链接

- https://www.cnblogs.com/wongbingming/p/13212306.html