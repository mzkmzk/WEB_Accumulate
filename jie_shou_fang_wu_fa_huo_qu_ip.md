# 接收方无法获取IP

# 问题

接收方一直在提示在获取ip中

然后我输入ifconfig也没有看到bridge100的信息(一般bridge100是Mac共享wifi的网卡)

估计是这玩意没启动起来

因为之前由于工作的原因需要修改网段(bridge100默认在192.168.2.1里),有些同学如果公司网段和这个冲突,那也得改bridge100的网段,一般是这样修改

```bash
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.nat NAT -dict-add SharingNetworkNumberStart 192.168.0.0
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.nat NAT -dict-add SharingNetworkNumberEnd 192.168.0.20
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.nat NAT -dict-add SharingNetworkMask 255.255.255.0
```

这个很简单,就是设置bridge100的起始ip和结束ip,还有子网掩码

具体的信息都会在`/Library/Preferences/SystemConfiguration/com.apple.nat.plist`里

然后我悲剧的发现我起始地址和结束地址设成了....

```bash
 42         <key>SharingNetworkMask</key>
 43         <string>255.255.255.0</string>
 44         <key>SharingNetworkNumberEnd</key>
 45         <string>192.168.2.254</string>
 46         <key>SharingNetworkNumberStart</key>
 47         <string>192.168.105.254</string>
```

估计是我之前手抖动...设置错了..

原因比较奇怪,这个文件好像不能手动改,

必须上文的命令行

现在设置成了

```bash
 42         <key>SharingNetworkMask</key>
 43         <string>255.255.255.0</string>
 44         <key>SharingNetworkNumberEnd</key>
 45         <string>192.168.2.20</string>
 46         <key>SharingNetworkNumberStart</key>
 47         <string>192.168.2.1</string>
```

bridge100也出来了


```bash
bridge100: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	options=3<RXCSUM,TXCSUM>
	ether 82:e6:50:80:47:64
	inet 192.168.2.1 netmask 0xffffff00 broadcast 192.168.2.255
	inet6 fe80::80e6:50ff:fe80:4764%bridge100 prefixlen 64 scopeid 0xb
	Configuration:
		id 0:0:0:0:0:0 priority 0 hellotime 0 fwddelay 0
		maxage 0 holdcnt 0 proto stp maxaddr 100 timeout 1200
		root id 0:0:0:0:0:0 priority 0 ifcost 0 port 0
		ipfilter disabled flags 0x2
	member: en0 flags=3<LEARNING,DISCOVER>
	        ifmaxaddr 0 port 4 priority 0 path cost 0
	nd6 options=1<PERFORMNUD>
	media: autoselect
	status: active
```

# 参考

1. 思路: http://ju.outofmemory.cn/entry/200076
2. 设置IP池: http://bbs.feng.com/read-htm-tid-10079720.html