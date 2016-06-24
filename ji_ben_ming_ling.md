# 基本命令

# 1. 入门

1. 查看nginx进程: `ps -ef|grep nginx`

  ```shell
  404@40401:~$ ps -ef|grep nginx
root      7996     1  0 Apr14 ?        00:00:00 nginx: master process nginx
404     14126  6118  0 11:56 pts/79   00:00:00 more nginx.conf
404     18278  7635  0 13:16 pts/88   00:00:00 grep --color=auto nginx
nobody   31455  7996  0 Jun01 ?        00:03:26 nginx: worker process
nobody   31456  7996  0 Jun01 ?        00:03:35 nginx: worker process
nobody   31457  7996  0 Jun01 ?        00:03:25 nginx: worker process
nobody   31458  7996  0 Jun01 ?        00:03:40 nginx: worker process
  ```
  
  这里进程的话主要由nobody执行,为什么是4个事因为在ngin.conf中设置了四核
  ```shell
  ## worker_processes的数值设置为cpu的核数
worker_processes  4;
## 绑定每个nginx进程所使用的CPU，使nginx可以利用cpu的多核
worker_cpu_affinity 0001 0010 0100 1000;
  ```

2. 检查配置文件的正确性: `sudo nginx -t`

   ```shell
   404@40401:~/env/nginx/default/conf$ sudo nginx -t
[sudo] password for work:
nginx: the configuration file 404/nginx.conf syntax is ok
nginx: configuration file 404/nginx.conf test is successful
   ```
3. 检查nginx完整配置信息: `sudo nginx -V`

    ```shell
    nginx -V
    ```
4. 启动nginx: 

  1. nginx
  2. nginx -s reload: 平滑启动nginx,表示不停止nginx的情况下重启nginx

# 参考链接

基本命令参考 <http://my.oschina.net/psuyun/blog/113694>