# 自动化性能测试探索之路

# 我们需要什么?

1. 白屏时间,首屏时间,DOM加载完成时间,window加载完成时间
2. 各浏览器和屏幕大小进行图片截图 - 暂放
3. 加载静态资源观察: https://github.com/jamesgpearce/confess
5. 页面加载视频回放 - 暂放
6. 各浏览器,可进行点击或其他交互事件,进行自动判断有误js错误,
7. 放入登录态 - 暂放

# 候选技术选型

1. berserkJS: 基于QT,c++编写
2. casperjs: 整合了PhantomJS,并写出一些好用的api
3. webpagetest: 自己搭建webpagetest(https://github.com/WPO-Foundation/webpagetest)