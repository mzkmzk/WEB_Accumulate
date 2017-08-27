# 入门

本文所在环境: Mac 10.10 chrome 60.0

chrome在我的机器上属主是root

解决: chown -R maizhikun /Users/maizhikun/Mac软件/Chrome/Google Chrome.app/

chrome没有在全局命令中 最好alias一下

```shell
vim ~/.bash_profile
...
alias chrome='/Users/maizhikun/Mac软件/Chrome/Google\ Chrome.app/Contents/MacOS/Google\ Chrome'

```

# 启动无头浏览器

chrome --headless --remote-debugging-port=9222

# 准备node跑页面

创建项目并 npm install --save chrome-remote-interface

然后跑新建如index.js, 然后node index.js 即可

```javascript
const CDP = require('chrome-remote-interface');

CDP((client) => {
  // Extract used DevTools domains.
  const {Page, Runtime} = client;

  // Enable events on domains we are interested in.
  Promise.all([
    Page.enable()
  ]).then(() => {
    return Page.navigate({url: 'https://example.com'});
  });

  // Evaluate outerHTML after page has loaded.
  Page.loadEventFired(() => {
    Runtime.evaluate({expression: 'document.body.outerHTML'}).then((result) => {
      console.log(result.result.value);
      client.close();
    });
  });
}).on('error', (err) => {
  console.error('Cannot connect to browser:', err);
});
```


# 参考文档

1. https://chromium.googlesource.com/chromium/src/+/lkgr/headless/README.md