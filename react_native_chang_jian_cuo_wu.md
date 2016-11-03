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
 
 # typeError
 
 界面显示的是 unhandler js TypeError
 
 然后xcode控制台显示
 
 [tid:main] Unhandled JS Exception: TypeError
 
 估计是因为缓存问题
 
 用`./node_modules/react-native/packager/packager.sh start --reset-cache`
 
 就可以了
 
 参考<http://stackoverflow.com/questions/35563025/new-react-native-app-has-typeerror-babelhelpers-typeof-is-not-a-function-ios>