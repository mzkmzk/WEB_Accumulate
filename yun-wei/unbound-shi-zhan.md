# unbound实战

# install

- windows

https://nlnetlabs.nl/projects/unbound/download/ 下载安装即可

tips 最好安装在`C:\Program Files\Unbound\`

因为我装在其他地方, 打开exe后 会自动去找`C:\Program Files\Unbound\service.conf`这个配置文件

安装之后的目录主要会有几个文件比较重要

```
- service.conf //主要配置文件
- unbound.exe //启动unbound的程序
```

# 查看当前路由器状态

因为unbound只是提供一个DNS服务, 所以连通服务还是由当前的路由器负责

先看下路由器的设置

例如iphone的查看路径为`设置->wi-fi->当前wifi->DHCP`

里面会列几个信息

```
IP地址: 10.0.11.5
子网掩码: 255.255.191.0
路由器: 10.0.0.252
DNS: 10.0.0.252
```

# 更改配置

安装完毕后, 打开service.conf 

主要修改`interface`为本机的IP

`access-control`要增加网段 根据路由器的I跑来

例如上面的路由器为`10.0.0.252`, 那么就设置为

`access-control: 10.0.0.0/16 allow`

local-data就写需要解析的域名跟IP即可

```javascript
server: auto-trust-anchor-file: "C:\Program Files\Unbound\root.key"
  interface: 192.168.65.175

	port: 53
	do-ip4: yes
	do-ip6: yes
	do-udp: yes
	do-tcp: yes
	do-daemonize: yes

	# allow access by the local network.

	access-control: 0.0.0.0/0 allow

	access-control: 192.168.0.0/16 allow

	access-control: 10.0.0.0/16 allow
	# top secret

	local-data: "mzk-pay.xunlei.com. IN A 192.168.65.29"

	local-data: "pay.xunlei.com. IN A 10.10.63.5"

	# login
```

# 手机配置wifi

还是以iphone为例例如iphone的查看路径为`设置->wi-fi->当前wifi->静态`

这里IP获取选择为静态 然后填写信息

根据上文的`查看当前路由器状态`填充内容

```
# 跟DHCP模式下设置相同的IP
IP: 10.0.11.5
# 前两段设置为DHCP下的数字, 后面两位置0
子网掩码: 255.255.0.0
# 设立跟DHCP设置一样的路由器
路由器: 10.0.0.252
# 这里设置为启动unbound的机器IP 
DNS: 192.168.65.175

到这里手机就能使用unbound的DNS辣

# 重启服务

修改了serve.conf的话需要重启unbound才能生效

`任务管理器->打开服务->Unbound DNS validator`

然后右键重启服务即可 