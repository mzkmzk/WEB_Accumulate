# 基本命令

# 1. 入门

1. 查看nginx进程: `ps -ef|grep nginx`

  ```shell
  work@TEST01:~$ ps -ef|grep nginx
root      7996     1  0 Apr14 ?        00:00:00 nginx: master process nginx
work     14126  6118  0 11:56 pts/79   00:00:00 more nginx.conf
work     18278  7635  0 13:16 pts/88   00:00:00 grep --color=auto nginx
nobody   31455  7996  0 Jun01 ?        00:03:26 nginx: worker process
nobody   31456  7996  0 Jun01 ?        00:03:35 nginx: worker process
nobody   31457  7996  0 Jun01 ?        00:03:25 nginx: worker process
nobody   31458  7996  0 Jun01 ?        00:03:40 nginx: worker process
  ```

# 参考链接

基本命令参考 <http://my.oschina.net/psuyun/blog/113694>