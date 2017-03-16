# onResourceRequested和onResourceReceived

# 背景

想收集一个页面的所有资源的大小及其数量

# 尝试方法

## onResourceRequested和onResourceReceived

先看下实验,然后再说明

```javascript
Resources.prototype.onResourceRequested = function(request){
    var now = new Date().getTime();
    this.resources[request.id] = {
        id: request.id,
        url: request.url,
        request: request,
        responses: {},
        duration: '-',
        times: {
            request: now
        }
    };
}

Resources.prototype.onResourceReceived = function(response){
    var now = new Date().getTime(),
        resource = this.resources[response.id];

    if (response.bodySize) {
        resource.size = response.bodySize;
    } else if (!resource.size) {
        response.headers.forEach(function (header) {
            if (header.name.toLowerCase()=='content-length') {
                resource.size = parseInt(header.value);
            }
        });
    }

    
}


```

## window.performance.getEntries()

`失败,放弃使用该方法`

在浏览器中,可利用`window.performance.getEntries()`获取所有资源的信息

但是发现phantomjs的window.performance没有提供这个方法

尝试

```javascript
 console.log('window.performance' + page.evaluate(function () {
    return JSON.stringify(window.performance, null, 4);
}));
```

输出

```javascript
window.performance{
    "navigation": {
        "type": 0,
        "redirectCount": 0
    },
    "timing": {
        "connectStart": 1489633757541,
        "navigationStart": 1489633757541,
        "secureConnectionStart": 0,
        "fetchStart": 1489633757541,
        "domContentLoadedEventStart": 1489633758839,
        "responseStart": 1489633757541,
        "domInteractive": 1489633758839,
        "domainLookupEnd": 1489633757541,
        "redirectStart": 0,
        "requestStart": 1489633757541,
        "unloadEventEnd": 0,
        "unloadEventStart": 0,
        "domComplete": 1489633759392,
        "domainLookupStart": 1489633757541,
        "loadEventStart": 1489633759392,
        "domContentLoadedEventEnd": 1489633758843,
        "redirectEnd": 0,
        "connectEnd": 1489633757541,
        "responseEnd": 1489633758111,
        "domLoading": 1489633758101,
        "loadEventEnd": 1489633759393
    }
}
```

因为JSON.stringify,只会输出属性,

而方法需要用for in获取

```javascript
console.log('window.performance ' + page.evaluate(function () {
    var array_ = [];
    for ( key in window.performance  ){
        array_.push(key)
    }
    return array_
}));
```
输出

```shell
window.performance navigation,timing,now
```

可枚举的方法只有now......其他两个是属性 所以放弃这个方法哒.