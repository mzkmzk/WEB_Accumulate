# php-fpm_setting

# 1. PHP环境

php-5.6.7

# 2. 常用命令

1. 查看php-fpm进程数目: `ps aux | grep -c php-fpm => 5`
2. 查看php-fpm配置路径: `ps aux | grep php-fpm`
3. 查看配置文件是否正确: `php-fpm  -t`
4. 可以在`php-fpm.conf`中找到pid对应的文件,里面会存着当前运行的PID
5. 启动php-fpm: `~/php/php-5.6.7/sbin`(具体每个人不一样)
6. 关闭php-fpm: kill -INT `对应的PID` | pkill php-fpm
7. 重启php-fpm: kill -USR2 ``对应的PID`
8. 查看php-fpm是否开启: `netstat -tunpl | grep 9000`(9000是默认)

# 参考链接

1. 
2. 

# 参考连接

1. php-fom.conf配置相关: <http://php.net/manual/zh/install.fpm.configuration.php>
2. php:fpm命令相关: <http://www.cnblogs.com/zdz8207/p/3765579.html>