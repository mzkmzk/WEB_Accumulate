# 命令长期运行

当用户注销/网络断开时,终端会收到HUP(hangup)信号从而关闭其所有子进程,所以解决方案有

1. 让进程忽略HUP信号
2. 让进程运行在新的会话里,从而不属于终端的子进程

# 1. nohup






# 参考链接

1. <https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/>