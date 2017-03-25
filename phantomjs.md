# page的on事件令其可多次使用

# 背景

page里的事件都是和DOM0类似

例如: page.onInitialized = function(){}

当我们有几个phantomjs js执行的js文件时,就被迫都耦合在一个文件

# 解决方案

利用promise

page.onInitialized只能被执行一次,而我们在其里面定义promise任务即可

1. 定义一个类似on_initialized_promise的数组,
2. 其他文件需要用到对应事件时,往on_initialized_promise数组push自己的方法
3. 等promise_page_event监听到on_initialized,进行统一处理

```javascript
var EVENT_NAME  = [
        'Initialized',//on_initialized_promise
        'LoadStarted',//on_loadstarted_promise
        'LoadFinished',//on_loadfinished_promise
        'UrlChanged',//on_urlchanged_promise
        'NavigationRequested',//on_navigationrequested_promise
        'RepaintRequested',//on_repaintrequested_promise
        'ResourceRequested',//on_resourcerequested_promise
        'ResourceReceived', //on_resourcereceived_promise
    ],
    Promise = require('es6-promise').Promise

var promise_page_event = function(page) {
     
    EVENT_NAME.forEach(function(element){
        var event_name_promise = 'on_' + element.toLowerCase() + '_promise';

        page[ event_name_promise ] = [];

        page[ 'on' + element ] = function(){
            var self_arguments = arguments;
            page[ event_name_promise ].reduce(function(promise, callback){
                return promise.then(function(){
                    if (typeof callback === 'function'){
                        return callback.apply(page, self_arguments)
                    }
                    return;
                })
            },Promise.resolve())
            
        };
    })

    return page;
}

module.exports =  promise_page_event;

```

在一个主文件里

```javascript
var page = require('webpage').create(),
    promise_page_event = require('./promise_page_event.js');

promise_page_event( page );
```

而其他文件需要用到page的事件时,只需把page传给他,然后

```javascript
page.on_initialized_promise.push(function(){
    console.log('injectJs get_data'+page.injectJs('../JS/get_data.js'));
})
``