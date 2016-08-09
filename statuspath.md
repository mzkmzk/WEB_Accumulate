# status_path

# 1. 开启status_path

1. 在php-fpm.conf中设置打开status_path路径
  `pm.status_path = /status`
2. nginx设置:
    在任一代理配置下设置
    
    ```shell
    location ~ ^/(status|ping)$
    {
        include fastcgi_params;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $fastcgi_script_name;
    }
    ```
3. 重启php-fpm和nginx
4. 查看status_path

    ```shell
    work@TEST01:~$ curl http://example.com/status
pool:                 www // 连接池的名字,一般叫www
process manager:      dynamic //进程管理
start time:           09/Aug/2016:13:31:40 +0800 //php-fpm启动时间
start since:          27750 //php-fpm启动了多少秒
accepted conn:        3070 //接受了多少链接
listen queue:         0 //请求等待队列,如果这个值不为0,那么要增加FPM的进程数量
max listen queue:     4 //在此次启动php-fpm后,最大的等待队列
listen queue len:     128
idle processes:       2
active processes:     1
total processes:      3
max active processes: 3
max children reached: 0
slow requests:        1
    ```

# 参考链接

1. https://easyengine.io/tutorials/php/fpm-status-page/

