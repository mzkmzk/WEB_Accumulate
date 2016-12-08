# POST请求



# Content-Type

首先无论什么Content-Type,内容都是在请求包里面,浏览器也不会做任何处理,只不过告诉服务器不一样的方式获取数据而已

## application/x-www-form-urlencoded

一般的提交内容格式为title=test&sub%5B%5D=1&sub%5B%5D=2&sub%5B%5D=3

jquery默认的post提交默认是application/x-www-form-urlencoded

## application/json

当设置成这个头的时候

Safari的默认Content-Type是application/json

safari为默认把post改为options请求,导致有些服务器没有支持这个options,就会导致400bad request

## multipart/form-data

在from表单并且enctype等于 multipart/form-data时

##text/plain

chrome的默认Content-Type

这个就是纯文本的方式提交