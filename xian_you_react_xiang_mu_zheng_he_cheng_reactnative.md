# 现有React项目整合成ReactNative

# 需要准备的工具(iOS)

1. xcode
2. cocoapods: `sudo gem install cocoapods` (附录错误1)

# 创建iOS项目

就是xcode新建一个项目

# 将react整合进iOS中

1. Cocoapods集成React Native

  在`.xcodeproj`的统计目录下输入
  
    1. pod init,就会生成Podfile文件
    2. 修改Podfile
    
        ```bash
        # Uncomment this line to define a global platform for your project
        # platform :ios, '9.0'
        
          target 'Journey' do
      # Comment this line if you're not using Swift and don't want to use dynamic frameworks
      use_frameworks!
      # Pods for Journey
      pod 'React', :path => '/Users/maizhikun/Learning/apache_sites/Journey_Front/node_modules/react-native', :subspecs => [
          'Core',
          'RCTImage',
          'RCTNetwork',
          'RCTText',
          'RCTWebSocket',
          # 添加其他你想在工程中使用的依赖。
        ]
          target 'JourneyTests' do
            inherit! :search_paths
            # Pods for testing
          end
          
            target 'JourneyUITests' do
              inherit! :search_paths
              # Pods for testing
            end
        end
        ```
        其实最重要的是`target 'Journey' do`里面的代码,其他都是pod init自动生成的,这里其中经历了些波折,见附录错误2
        
2. 


# 附录错误1

在`cocoapods: `sudo gem install cocoapods`时

报错

```bash
 pod install
Setting up CocoaPods master repo
[!] Unable to add a source with url `https://github.com/CocoaPods/Specs.git` named `master`.
You can try adding it manually in `~/.cocoapods/repos` or via `pod repo add`.
```

参考文章<https://github.com/CocoaPods/CocoaPods/issues/4293>

Go to ~/.cocoapods/repos and run git clone https://github.com/CocoaPods/Specs.git master

# 附录错误2

刚开始执行pod install时,会爆

```bash
Analyzing dependencies
[!] The dependency `React/Core (from `/Users/maizhikun/Learning/apache_sites/Journey_Front/node_modules/react-native`)` is not used in any concrete target.
The dependency `React/RCTImage (from `/Users/maizhikun/Learning/apache_sites/Journey_Front/node_modules/react-native`)` is not used in any concrete target.
The dependency `React/RCTNetwork (from `/Users/maizhikun/Learning/apache_sites/Journey_Front/node_modules/react-native`)` is not used in any concrete target.
The dependency `React/RCTText (from `/Users/maizhikun/Learning/apache_sites/Journey_Front/node_modules/react-native`)` is not used in any concrete target.
The dependency `React/RCTWebSocket (from `/Users/maizhikun/Learning/apache_sites/Journey_Front/node_modules/react-native`)` is not used in any concrete target.
```

刚开始我以为又是npm的锅,后来经过<https://github.com/facebook/react-native/issues/7775>

发现可能因为pod的版本不一样

这个原因是因为刚开始的podfile文件是我自己在网上粘贴的

```bash
platform :ios, “7.0”

# 取决于你的工程如何组织，你的node_modules文件夹可能会在别的地方。
# 请将:path后面的内容修改为正确的路径（一定要确保正确～～）。
pod 'React', :path => ‘./ReactComponent/node_modules/react-native', :subspecs => [
 'Core',
  'ART',
  'RCTActionSheet',
  'RCTAdSupport',
  'RCTGeolocation',
  'RCTImage',
  'RCTNetwork',
  'RCTPushNotification',
  'RCTSettings',
  'RCTText',
  'RCTVibration',
  'RCTWebSocket',
  'RCTLinkingIOS',
]
#需要的模块依赖进来便可，这里是为了举例子，列举所有的模块
```

所以错误信息都说了` is not used in any concrete target`

而我自己通过pod init发现的确比网上粘贴的多了target ..do 这一部分

所以最后pod init 然后再修改 podfile,发现真的ok了

# 参考文章

1. http://www.lcode.org/react-native-%E7%A7%BB%E6%A4%8D%E5%8E%9F%E7%94%9Fios%E9%A1%B9%E7%9B%AE57/