# 添加SSI功能

# 背景

后端同学有用SSI的习惯,我在本地开发时,老感觉不爽,于是自己把apache也折腾成有ssi的

# 步骤

> 去掉httpd.conf的注释

```shell
LoadModule ssl_module libexec/apache2/mod_ssl.so
```

默认是不开启的,前面加着#号,去掉就行

> 添加支持类型文件

还是在httpd.conf中 找到

```shell
    AddType text/html .shtml
    AddOutputFilter INCLUDES .shtml .html
```

默认也是加着#号,而且后缀只有.shtml,如果需要可以像上面那样把.html后缀也加上

> 重启apache

# 结果

笔者还是没折腾出来..不知道为啥..算了....反正只是为了在本地测试...

# 参考链接

1. http://jingyan.baidu.com/article/4ae03de323cc903eff9e6bea.html