# 兼容相关

## JS

### safari格式化日期错误

chrome可以支持`new Date('2017-04-06 14:05:05')`这样的日期

但是 Safari下会返回`Invalid Date`

需要格式化为`"2017/04/06 14:05:05`

所以将时间格式一下

`'2017-04-06 14:05:05'.replace(/\-/g, '/')`

参考链接

1. http://blog.csdn.net/zhoukun1008/article/details/51537862