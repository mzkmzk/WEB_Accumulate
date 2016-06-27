# 解决跨域问题

首先声明下,这种方法非常不好,但是由于只用在个人的实验性项目中,为了方便采取这种方式,后面会用另外的方式解决

# 1. 问题

在`journey.404mzk.com`中调用`inner.journey.404mzk.com`会出现跨域问题,浏览器会报错

`No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin '' is therefore not allowed access.`

# 2. 解决

改一下`apache.conf`

原本

```shell
<Directory />
        Options FollowSymLinks
        AllowOverride All
        Require all denied
</Directory>
```

改为

```shell
<Directory />
        Options FollowSymLinks
        Require all denied
        Header set Access-Control-Allow-Origin *
        #AllowOverride All
        #Require all denied
</Directory>
```

这样设置Header需要引入`mod_headers`模块

不然启动时,会报

```shell
AH00526: Syntax error on line 156 of /etc/apache2/apache2.conf:
Invalid command 'Header', perhaps misspelled or defined by a module not included in the server configuration
Action 'restart' failed.
```

引入`mod_headers`

`/etc/apache2/mods-enabled# ln -s ../mods-available/headers.load ./`
