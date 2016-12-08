# ajax库实现

# 额外知识

## If-Modified-Since

在请求头首部字段

`If-Modified-Since:Thu, 01 Dec 2016 04:30:16 GMT`

在实体首部字段会有对应的

`Last-Modified:Wed, 07 Dec 2016 08:07:22 GMT`

验证的是文件的最后修改时间,如果这两个值一致,则返回304,否则为200

## If-None-Match

在请求头首部字段

`If-None-Match:W/"57baa49e-63c6"`

在响应首部字段对应的

`ETag:W/"57baa49e-63c6"`

验证的是文件内容的MD5,如果这两个值一致,则返回304,否则为200


# 参考资源

1. if-modified-since: http://www.cnblogs.com/zh2000g/archive/2010/03/22/1692002.html