# nginx实战

# ssi中virtual实现先访问xx路径再访问yy路径

### 背景

nginx部署前端应用中, 前端静态代码放在`/code`目录下, 

而后端生成的页面片(ssi) 和配置js 都挂载在`/backend-create`中

html中使用ssi情况如下


```html
<!--#include virtual="/v2017/k_pc/ssi/notify.html"-->
```

### 解决方案

首先了解virtual这里的相对路径其实实惠重新访问我们的nginx层找响应内容的

详情看nginx文档

```
include
Includes the result of another request into a response. The command has the following parameters:
  virtual
    specifies an included request, for example:
    
    <!--# include virtual="/remote/body.php?argument=value" -->
    
    Several requests specified on one page and processed by proxied or FastCGI/uwsgi/SCGI/gRPC servers run in parallel. If sequential processing is desired, the wait parameter should be used.
```

所以我们再利用nginx的`try_files`实现先找XX目录, 再找YY目录

```
  location @ssi {
      root  /backend-create/swjsq.xxx.com;
  }

  location / {
    ssi on;
    ssi_silent_errors on;
    ssi_types text/shtml;
    root  /code;
    try_files $uri $uri/ @ssi;
  }
```
