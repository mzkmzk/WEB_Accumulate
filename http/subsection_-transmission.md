# 分段传输

# 背景

对于一些静态文件,比较优秀的做法是进行分段传输

# 本文设计的HTTP响应头

1. Connection: keep-alive
2. Transfer-Encoding: chunked
3. Content-Encoding: gzip
4. Content-Length
5. Accept-Ranges:bytes

# Connection: keep-alive

HTTP1.1默认必须全部是长连接

HTTP1.0 需要声明Connection: keep-alive才为长链接

浏览器重用已经打开的空闲持久连接,可以避开缓慢的三次握手,还可以避免遇上TCP慢启动的拥塞适应阶段

# Content-length

可用于告诉浏览器,此次链接的实际长度(假如Content-length比实际短,浏览器也只会截取Content-length的长度)

一般

先列举一下正常的demo

```javascript
console.log('listen http://127.0.0.1:9090');
require('net').createServer(function(sock) {
    console.log('go in  http://127.0.0.1:9090');
    sock.on('data', function(data) {
        sock.write('HTTP/1.1 200 OK\r\n');
        sock.write('\r\n');
        sock.write('hello world!');
        sock.destroy();
    });
}).listen(9090, '127.0.0.1');
```
1. 声明协议
2. 打印内容
3. 结束链接
4. 没毛病

而此时改成这样 结果会如何

```javascript
console.log('listen http://127.0.0.1:9090');
require('net').createServer(function(sock) {
    console.log('go in  http://127.0.0.1:9090');
    sock.on('data', function(data) {
        sock.write('HTTP/1.1 200 OK\r\n');
        sock.write('Content-Length: 11\r\n');
        sock.write('\r\n');
        sock.write('hello world!');
        //sock.destroy();
    });
}).listen(9090, '127.0.0.1');
```

改动地方

1. `Content-Length: 11`: 导致结果只会打印`hello world`,`!`无法输出,因为已经超出了Content-Length的长度
2. `//sock.destroy()`:  如果不设置content-length,该链接会一直显示pedding(chrome的network可看),但是声明了长度,这里的销毁就不重要了



