# 使用fetch

令我最爽的是,这个默认不带cookie,请求看起来都好多了

这里就写下最基本的写法吧

```javascript
fetch('http://inner.journey.404mzk.com/v1/Activity_Controller/query',{
          method: 'GET',
          body: JSON.stringify(params)
        }).then(function(result) {
            ...
        })        
```

这个浏览器兼容性不太好..

最好引用polyfill

`npm install --save whatwg-fetch`然后import进去哦
# 参考链接

1. https://github.com/camsong/blog/issues/2