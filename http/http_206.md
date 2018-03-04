# HTTP 206

# 简介

服务器已经成功处理了部分GET请求。类似于FlashGet或者迅雷这类的HTTP 下载工具都是使用此类响应实现断点续传或者将一个大文档分解为多个下载段同时下载。

# 相关Request字段

* Range:bytes=0-

语法: 

```bash
# first-byte-pos 和 last-byte-pos都可忽略,但不能同时忽略
Range: range-unit=first-byte-pos "-" [last-byte-pos]

```

指定内容的范围


# Report

* Accept-Ranges: bytes

表明了服务器支持Range请求，且支持的单位是bytes

* Content-Range: bytes 0-499/1234

语法

```bash
Content-Range: range-unit first-byte-pos "-" last-byte-pos "/" (instance-length | *)
```

instance-length可以表示当前资源的长度,也可以*表示不知道具体的实际长度值




# Response



# 参考资料

1. 挺不错的介绍文章: http://blog.aijc.net/server/2015/11/12/HTTP%E5%8D%8F%E8%AE%AE206%E7%8A%B6%E6%80%81%E7%A0%81