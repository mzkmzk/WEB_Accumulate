# ajax库实现

# 额外知识

## If-Modified-Since

在请求手首部字段

`If-Modified-Since:Thu, 01 Dec 2016 04:30:16 GMT`

在实体首部字段会有对应的

`Last-Modified:Wed, 07 Dec 2016 08:07:22 GMT`

如果这两个值一致,则返回304,否则为200

## If-None-Match

# 参考资源

1. if-modified-since: http://www.cnblogs.com/zh2000g/archive/2010/03/22/1692002.html