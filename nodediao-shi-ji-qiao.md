# Node调试技巧

在进行node开发时

简单的可以通过console解决

但是在解决一些复杂的问题的时候, 使用chrome调试器或者IDE中的调试器会更为高效

# 各种调试方式

## chrome调试 

1. 在chrome地址栏输入chrome://inspect
2. 点击`Open dedicated DevTools for Node`
3. 调试程序 node --inspect index.js或node --inspect-brk index.js

`--inspect`表示要进入调试模式(就是通过websocket输出数据到默认的端口9229 然chrome接口)

`--inspect-brk`表示在给第一行进行打点

## VS CODE调试

chrome调试的缺点在于不能一开始就对代码进行设置断点

必须先进入到浏览器调试器, 然后选中要调试的代码 

而IDE调试 是可以预先先点好代码行

![IDE调试](/assets/QQ20180212-160655.png)




# 待补充

1. 远程调试 https://i5ting.github.io/node-debug-tutorial/

#参考链接

1. node官网调试文档: https://nodejs.org/en/docs/inspector/