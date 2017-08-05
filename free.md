# free

#学习背景

在php中 应用composer install时

报错

`The following exception is caused by a lack of memory or swap, or not having swap configured`

额, 乍一眼看过去貌似是没有交换空间了 

后来初步学习了下free, 发现我的系统没有swap(可能我的主机就1GB内存, 阿里默认不帮我开?)

# 命令用法

参数 

```shell
-b 　以Byte为单位显示内存使用情况。 
-k 　以KB为单位显示内存使用情况。 
-m 　以MB为单位显示内存使用情况。
-g   以GB为单位显示内存使用情况。 
-o 　不显示缓冲区调节列。 
-s<间隔秒数> 　持续观察内存使用状况。 
-t 　显示内存总和列。 
-V 　显示版本信息。
```


# 其他

## 创建虚拟内存

```javascript
/bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
/sbin/mkswap /var/swap.1
/sbin/swapon /var/swap.1
```

这里是创建了1GB的swap

# 参考资源

1. composer install报错: https://getcomposer.org/doc/articles/troubleshooting.md#proc-open-fork-failed-errors
2. 简单说明: http://www.cnblogs.com/peida/archive/2012/12/25/2831814.html