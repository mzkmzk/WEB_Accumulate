# 常见错误

# 内核文件最大大小不足

```
{"code":"EMFILE","errno":"EMFILE","syscall":"watch null","filename":null}
```

解决方案

https://github.com/facebook/react-native/issues/9309

# IP出错

报错信息

```
 Assertion failure in -[RCTBatchedBridge loadSource:](),/node_modules/react-native/React/Base/RCTBatchedBridge.m:189
 ```
 
 解决方案<http://stackoverflow.com/questions/38780325/running-react-native-app-on-ios-device-using-offline-bundle>
 
 主要是因为IP的问题,把Xcode编译的版本从debug改成release即可