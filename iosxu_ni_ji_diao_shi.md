# iOS虚拟机/真机调试,及其坑点

## 1. 如何调试

1. 必须要安装Simulator(安装Xcode后自动绑定),然后启动Simulator

  快捷启动方式有
  
  1 .保存Simulator在Menu

  2. 命令行`/Applications/Xcode-beta.app/Contents/Developer/Applications/Simulator.app` ,每个人的路径可能不太一样,基本在安装Xcode下按Tab慢慢按出来,刚进Simulator,最好看看菜单栏上的Hardware和window之类的
2. 启动后,在手机Safari中打开需要调试的页面,然后在Mac中`Safari->开发->iOS Simulator`(若没有开发菜单,则票号设置->高级中开启),/真机类似
3. 

下面会记录一些坑点

## 2. 没弹出小键盘,不触发键盘事件

![小键盘](QQ20160428-2.png![](QQ20160428-3.png)

我们在这个页面设置了填写了验证码,就可以点击登录,但并没什么用,

必须要系统键盘

此时,取消掉`Hardware->keyboard->Connection Hardware keyboard`,虚拟机即可弹出小键盘,监听事件OK.


## 参考链接

淘宝前端: <http://taobaofed.org/blog/2015/11/13/web-debug-in-ios/>

比较全面: <http://yujiangshui.com/multidevice-frontend-debug/>

最云测试平台的网站(基本覆盖所有机型和平台的浏览器,但是速度比较卡) <www.browserstack.com>