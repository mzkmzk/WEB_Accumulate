# lighthouse

# 简介

lighthouse是一个

# Gather 收集数据部分

## 生命流程

简单版

1. 启动浏览器
2. 执行Gather
    1. beforePass: 访问url前的操作
    2. pass: load页面后的操作
    3. afterPass: 所以gather都执行完pass后

下面是官方版的

```js
/**
 * Class that drives browser to load the page and runs gatherer lifecycle hooks.
 * Execution sequence when GatherRunner.run() is called:
 *
 * 1. Setup
 *   A. navigate to about:blank
 *   B. driver.connect()
 *   C. GatherRunner.setupDriver()
 *     i. assertNoSameOriginServiceWorkerClients
 *     ii. beginEmulation
 *     iii. enableRuntimeEvents
 *     iv. evaluateScriptOnLoad rescue native Promise from potential polyfill
 *     v. register a performance observer
 *     vi. register dialog dismisser
 *     vii. clearDataForOrigin
 *
 * 2. For each pass in the config:
 *   A. GatherRunner.beforePass()
 *     i. navigate to about:blank
 *     ii. Enable network request blocking for specified patterns
 *     iii. all gatherers' beforePass()
 *   B. GatherRunner.pass()
 *     i. cleanBrowserCaches() (if it's a perf run)
 *     ii. beginDevtoolsLog()
 *     iii. beginTrace (if requested)
 *     iv. GatherRunner.loadPage()
 *       a. navigate to options.url (and wait for onload)
 *     v. all gatherers' pass()
 *   C. GatherRunner.afterPass()
 *     i. endTrace (if requested) & endDevtoolsLog & endThrottling
 *     ii. all gatherers' afterPass()
 *
 * 3. Teardown
 *   A. GatherRunner.disposeDriver()
 *   B. collect all artifacts and return them
 *     i. collectArtifacts() from completed passes on each gatherer
 *     ii. add trace data and computed artifact methods
 */
```

## Gather 例子1: 收集console信息

```javascript
'use strict';

const Gatherer = require('./gatherer');

class ChromeConsoleMessages extends Gatherer {

  constructor() {
    super();
    this._logEntries = [];
    this._onConsoleEntryAdded = this.onConsoleEntry.bind(this);
  }

  onConsoleEntry(entry) {
    this._logEntries.push(entry);
  }

  beforePass(options) {
    const driver = options.driver;
    driver.on('Log.entryAdded', this._onConsoleEntryAdded);
    return driver.sendCommand('Log.enable')
      .then(() => driver.sendCommand('Log.startViolationsReport', {
        config: [{name: 'discouragedAPIUse', threshold: -1}],
      }));
  }

  afterPass(options) {
    return Promise.resolve()
        .then(_ => options.driver.sendCommand('Log.stopViolationsReport'))
        .then(_ => options.driver.off('Log.entryAdded', this._onConsoleEntryAdded))
        .then(_ => options.driver.sendCommand('Log.disable'))
        .then(_ => this._logEntries);
  }
}

module.exports = ChromeConsoleMessages;
```

代码主要在beforePass中 

1. 监听console增加触发的事件: `driver.on('Log.entryAdded', this._onConsoleEntryAdded);`
2. 开启Logger功能: `driver.sendCommand('Log.enable')`
3. 开启chrome中自带的console警告功能: `driver.sendCommand('Log.startViolationsReport', {
  config: [{name: 'discouragedAPIUse', threshold: -1}],
}`
      
然后在afterPass 主要就是关闭在beforePass中开启的功能




# 常用概念名称

1. First Paint Time(FP): 表示文档中任一元素首次渲染的时间
2. First Contentful Paint Time(FCP): 文档中内容元素(文本、图像、Canvas或者SVG)首次渲染的时间(通常是无意义的渲染, 例如头部和导航栏)
3. First Meaningful Paint Time(FMP): 代表首次有意义的渲染时间(它的统计在重大布局变化之后, 往往代表了用户所关心的首次渲染时间)
4. First Interactive Time(FI): 首次可交互时间
5. Consistently Interactive Time(CI): 持续可交互时间
6. Fisrt Visual Change: 首次视觉发生变化的时间点
7. Last Visual Change: 最后一次视觉发生变化的时间点
8. Speed Index: 视觉速度(Mean Pixel-Histogram Difference 算法)
9. Perceptual Speed Index: 视觉速度(Structural Similarity Image Metric算法)


# 参考链接

1. 常用概念名称: https://tech.meituan.com/Optimization_of_front_end_sensory_properties.html?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io