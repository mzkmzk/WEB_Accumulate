# nc命令

## 简介

一个使用`tcp`/`udp`协议跨网络连接读取和写入数据的工具

## 基本语法

```bash
nc [options] host port
```

参数说明

```bash
- v: 让nc提供更多详细输出
- z: 只扫描监听的`daemons`, 而不发送数据给他们
```

## 实战常用

### 扫描端口打开情况

```bash
~ nc -zv 0.0.0.0 3098-3100
nc: connectx to 0.0.0.0 port 3098 (tcp) failed: Connection refused
nc: connectx to 0.0.0.0 port 3099 (tcp) failed: Connection refused
Connection to 0.0.0.0 port 3100 [tcp/opcon-xps] succeeded!
```

### 发送内容给制定ip和端口

```
echo -ne "GET / HTTP/1.0\r\n\r\n" | nc www.baidu.com 80
```

## 参考

- [nc简单说明](https://linuxize.com/post/netcat-nc-command-with-examples/)