# 分段传输的由来

# 背景

对于一些静态文件,比较优秀的做法是进行分段传输

1. 因为减少三次握手,所以引用了Connection: keep-alive
2. 因为Connection: keep-alive,需要知道实体的边界,所以需要Content-Length
3. 因为Content-Length计算动态文件比较麻烦,所以运用了Transfer-Encoding: chunked

# 本文设计的HTTP响应头

1. Connection: keep-alive
2. Transfer-Encoding: chunked
3. Content-Encoding: gzip: 表名启动gzip压缩
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


应用场景

1. 静态文件: 例如img

不适用于:动态文件

因为计算Content-length需要开启buffer,等全部内容生成好再计算,这样会影响TTFB(客户端发出请求到接受到响应第一个字节所花费的时间)

但是为了计算响应实体的长度而缓存所有内容,跟更短的TTFB背道而驰

所以Transfer-Encoding: chunked的出现,可以不需要计算Content-Length

# Transfer-Encoding: chunked

声明了Transfer-Encoding: chunked后,主题内容有规则

1. 每个分块包括两行,一行十六进制的长度值,第二行为数据
2. 两行都需要`\r\n`结尾
3. 最后一块的长度值必须为0,对应的分块数据没有内容

node模拟

```javascript
require('net').createServer(function(sock) {
    sock.on('data', function(data) {
        sock.write('HTTP/1.1 200 OK\r\n');
        sock.write('Transfer-Encoding: chunked\r\n');
        sock.write('\r\n');

        sock.write('b\r\n');
        sock.write('01234567890\r\n');

        sock.write('5\r\n');
        sock.write('12345\r\n');

        sock.write('0\r\n');
        sock.write('\r\n');
    });
}).listen(9090, '127.0.0.1');
```

输出是: 0123456789012345

(假如十六进制比实际内容小,则资源会获取失败,而比实际内容大,则正常)

# Accept-Ranges:bytes

之前的Transfer-Encoding: chunked还是属于一个链接的分块

假如视频这些大文件,需要把视频分成一小块一小块返回,例如腾讯视频可以去看看视频资源都是Accept-Ranges:bytes的效应头

响应头Accept-Ranges:bytes,表名切块根据bytes来

而响应头也有对应的Range:bytes=0-

表明从多少字节开始

状态码也会变成206

# 参考文章

1. https://imququ.com/post/transfer-encoding-header-in-http.html: 主要参考这里面的思路走