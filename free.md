# free

#学习背景

在php中 应用composer install时

报错

`The following exception is caused by a lack of memory or swap, or not having swap configured`

额, 乍一眼看过去貌似是没有交换空间了 

后来初步学习了下free, 发现我的系统没有swap(可能我的主机就1GB内存, 阿里默认不帮我开?)

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
2. 