# 小实验室

## 页面加载时间

检测方法

1. page.onLoadStarted和page.onLoadFinished方法的时间差
2. pag.open(url,回调函数),回调函数的时间戳

```javascript


"use strict";
var page = require('webpage').create(),
    system = require('system'),
    t_event, t_event_start,t_event_end,
    t, address;

//通过事件算加载时间
page.onLoadStarted = function() {
    t_event_start = Date.now()
    console.log("page.onLoadStarted");
    printArgs.apply(this, arguments);
};
page.onLoadFinished = function() {
    t_event_end = Date.now()
     console.log('Loading time event' +  ( t_event_end - t_event_start ) + ' msec');
    console.log("page.onLoadFinished");
    printArgs.apply(this, arguments);
};

if (system.args.length === 1) {
    console.log('Usage: loadspeed.js <some URL>');
    phantom.exit(1);
} else {
    t = Date.now();

    address = system.args[1];
    //通过回调函数算加载时间
    page.open(address, function (status) {


        if (status !== 'success') {
            console.log('FAIL to load the address');
        } else {
            t = Date.now() - t;
            console.log('Page title is ' + page.evaluate(function () {
                return document.title;
            }));
            console.log('Loading time ' + t + ' msec');
           
        }
        phantom.exit();
    });
    
}

```

结果

```shell
page.onLoadStarted
Loading time event 690 msec
page.onLoadFinished
Loading time 691 msec
```

时间基本一致