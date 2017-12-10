# 探究更优的Web调试体验

# 背景

作为一名Web开发者, 虽然我们要尽可能的避免进去调试

但是当出现问题时, 我们除了思考代码, 能做的就是调试

Web无处不在, 给Web调试带来不少的困扰

Web应用场景

1. 高级浏览器Chrome
3. 桌面类混合应用
4. iOS safari浏览器
5. iOs混合应用页面
6. android chrome浏览器
7. android混合应用页面

Web所在环境

1. 调试环境
2. 正式环境 

连接方式

1. 代理
2. 加载调试JS

# 想要用调试器做什么

1. 查看浏览器有无报错
2. 断点调试
3. 看自己的console.log()打点
4. 查看元素定位问题
5. 查看cache和localStroge存储内容
6. 静态资源的加载和接口的返回
7. 输入javascript 调试

当然还有一些像vue和react 他们调试中更加依赖自己生态中的vuetron和redux-devtools调试, 这里不多讨论

# 目标

为了更优雅的调试方式

1. 我们最想得到的还是chrome式的调试, 在前戏不麻烦的情况下
2. 在设置代理的情况下, 我们走代理和调试
3. 只需搓几下, 就可以简单的查看页面资源和调试面板

# Chrome式调试

### 假如在chrome

那就直接开发者工具使用就好了...

### 假如在android的chrome或混合APP

前戏:

1. 翻墙
2. 手机通过USB电缆连接电脑
3. android 系统设置 同意debug
4. (混合APP步骤): 需APP打包允许android调试https://developers.google.com/web/tools/chrome-devtools/remote-debugging/webviews?hl=zh-cn
5. 本机chrome输入 chrome://inspect

![chrome调试](/assets/1493428622.jpg)

> 偶尔会遇到chrome://inspect白屏问题..然后解决步骤是

1. chrome://appcache-internals/# 然后remove掉所有
2. 访问: https://chrome-devtools-frontend.appspot.com/ 是否可以通

### 调试iOS safari

1. iOS: 系统设置->Safari->高级->开启web检查器
2. mac safari: 偏好设置->高级->勾选在菜单栏中显示"开发菜单"
3. USB电缆连接mac和iOS设备
4. mac safari中safari中选择在iOS safari上打开的网页即可

![mac safari移动调试](/assets/2363263-9b5f0e4ce311e25b.png)

具体可参看: <http://www.jianshu.com/p/ed4b1bfb57dc>

# 代理式调试器

我们移动侧在内网调试时 要设置代理 

一般是手机走电脑中Finddler或`[spy-debugger](https://github.com/wuchangming/spy-debugger)`或其他NODE代理

个人顶一手[spy-debugger](https://github.com/wuchangming/spy-debugger)

它除了可以做代理

还可以进行样式和接口抓包并支持HTTPS

基本操作步骤即使

1. pc中: sudo spy-debugger
2. 手机设置代理即可

![样式调试](/assets/demo.png)

![抓包](/assets/AnyProxy.png)

# 前端插件

是说在页面可以通过一个小按钮 然后通过js本身的功能去实现调试

可以选择的有

[vConsole](https://github.com/Tencent/vConsole)

![vConsole](/assets/QQ20171210-161055.png)

[erud](https://github.com/liriliri/erud)

![erud](/assets/QQ20171210-161216.png)

两款来说 erud第一眼感觉还是不错的

功能很多 但是试用了一下 还是有点问题的 所以还是建议使用比较稳定的vConsole

# 总结

在混合应用开发时

一般的操作都是会在类chrome下调试得差不多, 然后再放过去混合应用中

所以类vConsole 就比较能处理一些普通的异常情况

chrome式的调试和代理模式 还是要稍微准备下USB或代理的 所以会稍微麻烦一点

# 待补充

1. 四指联调法

# 参考链接

1. Weinre移动端调试: https://div.io/topic/1322
2. 各调试方式大全: https://github.com/jieyou/remote_inspect_web_on_real_device