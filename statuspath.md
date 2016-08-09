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

