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

打印的结果是

onResourceRequested被回调了一次

而onResourceReceived被回调了两次

看下结果的输出

```javascript
{
    "headers": [

        {
            "name": "User-Agent",
            "value": "Mozilla/5.0 (Macintosh; Intel Mac OS X) AppleWebKit/538.1 (KHTML, like Gecko) PhantomJS/2.1.1 Safari/538.1"
        },
        {
            "name": "Accept",
            "value": "*/*"
        }
    ],
    "id": 20,
    "method": "GET",
    "time": "2017-03-16T03:14:58.266Z",
    "url": "http://**/img/banner/201702151504372411.jpg"
}
{
    "body": "",
    "bodySize": 8333,
    "contentType": "image/jpeg",
    "headers": [
        {
            "name": "Content-Length",
            "value": "35512"
        },
       
        {
            "name": "Accept-Ranges",
            "value": "bytes"
        },
    ],
    "id": 20,
    "redirectURL": null,
    "stage": "start",
    "status": 200,
    "statusText": "OK",
    "time": "2017-03-16T03:14:59.334Z",
    "url": "http://**/img/banner/201702151504372411.jpg"
}
{
    "contentType": "image/jpeg",
    "headers": [
       
        {
            "name": "Content-Length",
            "value": "35512"
        },
        {
            "name": "Accept-Ranges",
            "value": "bytes"
        }
    ],
    "id": 20,
    "redirectURL": null,
    "stage": "end",
    "status": 200,
    "statusText": "OK",
    "time": "2017-03-16T03:14:59.364Z",
    "url": "http://**/img/banner/201702151504372411.jpg"
}
```

第一次response和第二次response的差别主要有 

1. 第一次response多一个`"body": ""和"bodySize": 8333`
2. 第二次的stage为end,而第一次stage为start

只有当headers中含有

```shell
{
    "name": "Accept-Ranges",
    "value": "bytes"
}
```
时,才会出现第一次的bodySize和content-length不一致的情况

因为`Accept-Ranges: bytes`表明资源可以分段传输

而`Accept-Ranges`和`Content-length`是服务器决定是否给你返回这个headers的

而`Accept-Ranges`和`Content-length`是配套使用的

因为没有Content-length无法知道断点传输是否已经传输完了

> 结论

1. bodySize: 可能只包含部分字节(size of the received content decompressed (entire content or chunk content))
2. 获取资源大小,先获取content-length,没有再去找bodysize

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

# 注意点

1. PhantomJS是一个阉割版的webkit，不支持flash、webGL、video/audio、css 3-d，phontomjs不想背负操作系统强相关的特性，跨平台比较困难。
2. 如果使用Page模块的onResourceReceived()方法监听页面收到的请求资源，是无法得到该资源的response.body的，这也是目前PhantomJS最受开发者吐槽的点之一。

# 参考资源

1. http://imweb.io/topic/560b402ac2317a8c3e08621c: phantomjs不能做什么