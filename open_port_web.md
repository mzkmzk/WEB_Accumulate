# 对外开放网络端口

# 背景

想搭建一个内部Npm,在本机的Mac和Ubuntu上,直接搭建完毕,然后监听端口即可

但是在红帽中

```shell
 cat /proc/version
Linux version 2.6.32-431.el6.x86_64 (mockbuild@c6b8.bsys.dev.centos.org) (gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) ) #1 SMP Fri Nov 22 03:15:09 UTC 2013
```

本地能curl到,但是外网访问不到

# 端口开放限制

1. 添加4873可访问端口: /sbin/iptables -I INPUT -p tcp --dport 4873 -j ACCEPT
2. 保存路由表: /etc/rc.d/init.d/iptables save
3. 查看路由表情况

   ```shell
   /etc/init.d/iptables status
表格：filter
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           tcp dpt:4873
2    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED
3    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
4    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
5    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22
6    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:80
7    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:443
8    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:3306
9    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:6379
10   REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited

   Chain FORWARD (policy ACCEPT)
   num  target     prot opt source               destination
   1    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited
   
   Chain OUTPUT (policy ACCEPT)
   num  target     prot opt source               destination
   
   ```

# 参考链接

1. http://blog.sina.com.cn/s/blog_3eba8f1c0100tsox.html