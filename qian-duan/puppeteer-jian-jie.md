# Puppeteer 简介 

# 简述

Puppeteer是一个Node库, 提供了基于devtools协议上的高级API, 用于操控Chrome或Chromium

Puppeteer 建立于 chrome 能headless运行+ chromedevtools协议 

chromedevtools协议 https://chromedevtools.github.io/devtools-protocol/

命令行启动 无头模式 并开始 remote

```bash
chrome --headless --remote-debugging-port=9222 https://chromium.org
```

chromedevtools 其实好早就有了 但是headless 是2017年 chrome 59才支持的

而说了 Puppeteer 是devtools协议上的高级API

当然也有底层的devtools协议的库 例如 https://github.com/cyrus-and/chrome-remote-interface 这里不多做介绍

# 作用

- 集成测试
- 繁琐的浏览器任务
- 爬虫
- 性能

# 集成测试 

建议集成测试 可以配合 jest+pupputeer 来进行集成测试

可以先按https://jestjs.io/docs/en/puppeteer 的这个文档进行 配置 

具体案例可见

```javascript
let page

beforeAll(async () => {
    page = await global.__BROWSER__.newPage()
    await page.goto('http://m.pay.xxx.com/vippay/xxx.html')
})

it(' test W4 下单', async () => {
    expect.assertions(1)
    
    await page.click('.j_payway_tab[data-key=old_payway_w4]')

    return new Promise(resolve => {
        page.on('response', async (response) => {
            if ( response.url().indexOf('https://xxxxxxx.com/payorder/v3/Order') !== -1){
                let responseJson = await response.json()
                expect(responseJson.data.orderId.length > 0).toBe(true)
                resolve()
            }
        })
    })
})
```

然后执行jest 即可进行集成测试

很多开发 不要认为测试只是测试的事

每个开发都应该争取在同等单位的开发时间内, 能少更多的bug

# 繁琐的浏览器任务

# 爬虫 

# 性能

# 源码分析

大致框架 

图片来源 https://github.com/GoogleChrome/puppeteer/

![puppeteer大致框架](../assets/40333229-5df5480c-5d0c-11e8-83cb-c3e371de7374.png)

其主要代码目录结构为

```bash
.
├── Errors.js
├── index.js // puppeteer 主入口
├── install.js // 安装 Chromium核
└── lib/
    ├── Accessibility.js
    ├── Browser.js
    ├── BrowserFetcher.js
    ├── Connection.js
    ├── Coverage.js
    ├── DOMWorld.js
    ├── DeviceDescriptors.js
    ├── Dialog.js
    ├── EmulationManager.js
    ├── Errors.js
    ├── Events.js
    ├── ExecutionContext.js
    ├── FrameManager.js
    ├── Input.js
    ├── JSHandle.js
    ├── Launcher.js
    ├── LifecycleWatcher.js
    ├── Multimap.js
    ├── NetworkManager.js
    ├── Page.js
    ├── PipeTransport.js
    ├── Puppeteer.js
    ├── Target.js
    ├── TaskQueue.js
    ├── TimeoutSettings.js
    ├── Tracing.js
    ├── USKeyboardLayout.js
    ├── WebSocketTransport.js
    ├── Worker.js
    ├── api.js // 简单罗列所有class对象 方便hook async 报错时 能 new Error错误栈
    └── helper.js
```

# 代码小技巧

