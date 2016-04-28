# iOS虚拟机/真机调试,及其坑点

## 1. 如何调试

1. 必须要安装Simulator(安装Xcode后自动绑定),然后启动Simulator

  快捷启动方式有
  
  1 .保存Simulator在Menu

  2. 命令行`/Applications/Xcode-beta.app/Contents/Developer/Applications/Simulator.app` ,每个人的路径可能不太一样,基本在安装Xcode下按Tab慢慢按出来
2. 启动后,在手机Safari中打开需要调试的页面,然后在Mac中`Safari->开发->iOS Simulator`(若没有开发菜单,则票号设置->高级中开启),/


## 参考链接

<http://taobaofed.org/blog/2015/11/13/web-debug-in-ios/>

<http://yujiangshui.com/multidevice-frontend-debug/>