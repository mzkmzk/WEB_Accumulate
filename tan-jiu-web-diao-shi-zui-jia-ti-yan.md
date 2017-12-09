# 探究Web调试最佳体验

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

高级浏览器chrome类的调试方式就不多说了, 是比较得心应手的

# 想要用调试器做什么

1. 查看浏览器有无报错
2. 断点调试
3. 看自己的console.log()打点
4. 查看元素定位问题
5. 查看cache和localStroge存储内容
6. 静态资源的加载和接口的返回
7. 输入javascript 调试

当然还有一些像vue和react 他们调试中更加依赖自己生态中的vuetron和redux-devtools调试, 这里不多讨论



# 工具

1. 移动端调试: https://github.com/liriliri/eruda

# 参考链接

1. Weinre移动端调试: https://div.io/topic/1322
2. 各调试方式大全: https://github.com/jieyou/remote_inspect_web_on_real_device