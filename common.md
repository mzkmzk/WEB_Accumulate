# Common

##1.Write failed: Broken pipe

`ssh`一段时间后不操作,会出现如题错误.

1. 个人机配置

    创建`./.ssh/config`文件
    
    ```shell
    ServerAliveInterval 60
    ```
    
2. or 服务器配置

    `/etc/ssh/sshd_config`
    
    ```shell
    ClientAliveInterval 60
    ```
    
参考链接<http://www.cnblogs.com/dudu/archive/2013/02/07/ssh-write-failed-broken-pipe.html>