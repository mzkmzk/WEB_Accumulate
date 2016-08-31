# 接收方无法获取IP

# 问题

接收方一直在提示在获取ip中

然后我输入ifconfig也没有看到bridge100的信息(一般bridge100是Mac共享wifi的网卡)

估计是这玩意没启动起来

因为之前由于工作的原因需要修改网段(bridge100默认在192.168.2.1里),有些同学如果公司网段和这个冲突,那也得改bridge100的网段,一般是这样修改

```shell
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.nat NAT -dict-add SharingNetworkNumberStart 192.168.0.0
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.nat NAT -dict-add SharingNetworkNumberEnd 192.168.0.20
sudo defaults write /Library/Preferences/SystemConfiguration/com.apple.nat NAT -dict-add SharingNetworkMask 255.255.255.0
```

这个很简单,就是设置bridge100的起始ip和结束ip,还有子网掩码

具体的信息都会在`/Library/Preferences/SystemConfiguration/com.apple.nat.plist`里

然后我悲剧的发现我起始地址和结束地址设成了....

```shell
 42         <key>SharingNetworkMask</key>
 43         <string>255.255.255.0</string>
 44         <key>SharingNetworkNumberEnd</key>
 45         <string>192.168.2.254</string>
 46         <key>SharingNetworkNumberStart</key>
 47         <string>192.168.105.254</string>
```

估计是我之前手抖动...设置错了..
