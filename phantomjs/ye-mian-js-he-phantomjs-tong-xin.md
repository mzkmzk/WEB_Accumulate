# 页面JS和phantomjs通信

# 需求

1. phantomjs->page
2. page->phantomjs


# phantomjs->page

## evaluate

```javascript
page.evaluate(function(atf){
        window.judge_picture_evaluate(atf); 
},_atf)
```

# page->phantomjs

## serve

```javascript
//phantomjs
server.listen(10220, function(req, res){
    res.statusCode = 200;
    res.headers = {
        'Access-Control-Allow-Origin': '*' //这个很重要哈 不然会出现跨域问题
    };
    res.write(JSON.stringify(req, null, 4));
    res.close();
})

//javascript
$.ajax({
  type: "get",
  timeout: 3000,
  url: 'http://127.0.0.1:10220',
  dataType: 'json',
  data: {},
  success: function(response){
                 alert('收到回复的请求')
                 alert(JSON.stringify(response));

  },
   error: function(result){alert('ajax error'+ JSON.stringify(result))}
});

```

结果

```shell
//serve收到请求
server {
  "headers": {
    "Accept": "application/json, text/javascript, */*; q=0.01",
    "Accept-Encoding": "gzip, deflate",
    "Accept-Language": "zh-CN,en,*",
    "Connection": "Keep-Alive",
    "Host": "127.0.0.1:10220",
    "Origin": "http://xunlei.com",
    "Referer": "http://xunlei.com/",
    "User-Agent": "Mozilla/5.0 (Macintosh; Intel Mac OS X) AppleWebKit/538.1 (KHTML, like Gecko) PhantomJS/2.1.1 Safari/538.1"
  },
  "httpVersion": "1.1",
  "method": "GET",
  "url": "/"
}

//javascript收到回复
onAlert{"0":"收到回复的请求"}
onAlert{"0":"{\"headers\":{\"Accept\":\"application/json, text/javascript, */*; q=0.01\",\"Accept-Encoding\":\"gzip, deflate\",\"Accept-Language\":\"zh-CN,en,*\",\"Connection\":\"Keep-Alive\",\"Host\":\"127.0.0.1:10220\",\"Origin\":\"http://xunlei.com\",\"Referer\":\"http://xunlei.com/\",\"User-Agent\":\"Mozilla/5.0 (Macintosh; Intel Mac OS X) AppleWebKit/538.1 (KHTML, like Gecko) PhantomJS/2.1.1 Safari/538.1\"},\"httpVersion\":\"1.1\",\"method\":\"GET\",\"url\":\"/\"}"}
onAlert{"0":"linster error catch"}
```


# 参考链接

1. http://www.barretlee.com/blog/2015/09/25/move-on-phantomjs/: serve通信