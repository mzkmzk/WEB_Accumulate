# 命令长期运行

当用户注销/网络断开时,终端会收到HUP(hangup)信号从而关闭其所有子进程,所以解决方案有

1. 让进程忽略HUP信号
2. 让进程运行在新的会话里,从而不属于终端的子进程

tips: 

1. 可以输入`jobs`来显示当前命令终端相关的任务

# 1. nohup

直接 nobup 命令 & 

`&`用来把命令放在后台接着把控制器交给用户

会默认把命令的输出放到`nohup.out`里

可以通过` nohup  ping grouplus.com  > pso.file 2>&1 &`来更改输出文件

ok但是我们怎么关闭呢....?

```shell
work@iZ94fnej0x9Z:~$ ps -ef
...
work     11177     1  0 Oct13 ?        00:00:05 ping baidu.com
work     11179     1  0 Oct13 ?        00:00:05 ping baidu.com
work     11187     1  0 Oct13 ?        00:00:04 ping baidu.com
work     11198     1  0 Oct13 ?        00:00:04 ping baidu.com
work     11259     1  0 Oct13 ?        00:00:05 ping baidu.com
...
#然后逐个kill掉.....
kill -9 11259
```

# 2. setsid

这个就是第二个解决方案

把命令运行在新的会话中

```shell
NAME
       setsid - run a program in a new session
```

运行一下试试

```shell
work@iZ94fnej0x9Z:~$ setsid ping xunlei.com
#在另外一个窗口,因为上面的窗口在不断输出ping记录...,而且ctrl+c不能终止....
work@iZ94fnej0x9Z:~$ ps -ef | grep xunlei.com
work     13067     1  0 09:37 ?        00:00:00 ping xunlei.com
work     13069 12865  0 09:38 pts/1    00:00:00 grep --color=auto xunlei.com
```

可以看看父进程是1(init进程的ID)

我就一直在setsid执行了之后,,一直关不掉输出.......

# 3 ()

将一个或多个命令包在()中,就能让这些命令运行在子shell中

# 4 disown

之前是在执行命令的时候把命令放到后台执行 

但是如果命令已经开始执行了呢 ?就需要disown了

基本参数

```shell
用disown -h jobspec来使某个作业忽略HUP信号。
用disown -ah 来使所有的作业都忽略HUP信号。
用disown -rh 来使正在运行的作业忽略HUP信号。
```

使用也分 执行命令时有加&和没加&

1. 有加&

  ```shell
  [root@pvcent107 build]# cp -r testLargeFile largeFile &
[1] 4825
[root@pvcent107 build]# jobs
[1]+  Running                 cp -i -r testLargeFile largeFile &
[root@pvcent107 build]# disown -h %1
[root@pvcent107 build]# ps -ef |grep largeFile
root      4825   968  1 09:46 pts/4    00:00:00 cp -i -r testLargeFile largeFile
root      4853   968  0 09:46 pts/4    00:00:00 grep largeFile
[root@pvcent107 build]# logout
  ```
2. 没用&

   ```shell
    [root@pvcent107 build]# cp -r testLargeFile largeFile2
    #这里按ctrl+z,把任务挂起
  [1]+  Stopped                 cp -i -r testLargeFile largeFile2
  [root@pvcent107 build]# bg %1 // 把任务放进后台
  [1]+ cp -i -r testLargeFile largeFile2 &
  [root@pvcent107 build]# jobs
  [1]+  Running                 cp -i -r testLargeFile largeFile2 &
  [root@pvcent107 build]# disown -h %1
  [root@pvcent107 build]# ps -ef |grep largeFile2
  root      5790  5577  1 10:04 pts/3    00:00:00 cp -i -r testLargeFile largeFile2
  root      5824  5577  0 10:05 pts/3    00:00:00 grep largeFile2
  [root@pvcent107 build]# 
   ```









# 参考链接

1. 后台不关闭使用: <https://www.ibm.com/developerworks/cn/linux/l-cn-nohup/>