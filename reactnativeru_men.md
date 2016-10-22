 # 入门

# 环境搭建

1. sudo npm install -g react-native-cli
2. 安装好Xcode
3. 切换淘宝源,不然npm很慢

  ```shell
  //default
   ~/Learning/iOS_APP: npm config get registry
https://registry.npmjs.org/
 ~/Learning/iOS_APP: npm config get disturl
undefined
  //设置
  npm config set registry https://registry.npm.taobao.org --global
npm config set disturl https://npm.taobao.org/dist --global
  ```
  
# 第一个ReactNative iOS App

```shell
react-native init AwesomeProject
...
To run your app on iOS:
   cd /Users/maizhikun/Learning/iOS_APP/AwesomeProject
   react-native run-ios
   - or -
   Open /Users/maizhikun/Learning/iOS_APP/AwesomeProject/ios/AwesomeProject.xcodeproj in Xcode
   Hit the Run button
To run your app on Android:
   Have an Android emulator running (quickest way to get started), or a device connected
   cd /Users/maizhikun/Learning/iOS_APP/AwesomeProject
   react-native run-android
   
   
cd AwesomeProject
react-native run-ios
```

# 参考文档

1. <https://facebook.github.io/react-native/>