# 现有React项目整合成ReactNative

# 需要准备的工具(iOS)

1. xcode
2. cocoapods: `sudo gem install cocoapods` (附录错误1)

# 创建iOS项目

就是xcode新建一个项目

# 将react整合进iOS中

# 附录错误1

在`cocoapods: `sudo gem install cocoapods`时

报错

```shell
 pod install
Setting up CocoaPods master repo
[!] Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master`.
You can try adding it manually in `~/.cocoapods/repos` or via `pod repo add`.
```

参考文章<https://github.com/CocoaPods/CocoaPods/issues/4293>

Go to ~/.cocoapods/repos and run git clone https://github.com/CocoaPods/Specs.git master

# 参考文章

1. http://www.lcode.org/react-native-%E7%A7%BB%E6%A4%8D%E5%8E%9F%E7%94%9Fios%E9%A1%B9%E7%9B%AE57/