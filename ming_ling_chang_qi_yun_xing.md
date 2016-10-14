# 命令长期运行

当用户注销/网络断开时,终端会收到HUP(hangup)信号从而关闭其所有子进程,所以解决方案有

1. 让进程忽略HUP信号
2. 让进程运行在新的会话里,从而不属于终端的子进程

# 1. nohup

直接 nobup 命令 & 

`&`用来把命令放在后台接着把控制器交给用户

会默认把命令的输出放到`nohup.out`里




# 参考链接

1. 后台不关闭使用: <https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/>