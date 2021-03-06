# 捕抓错误

# 背景

捕抓错误一般是

1. phantom.onError: 对phantomjs执行的js进行捕抓
2. pageonError: 对页面中的js进行错误捕抓
3. page中的资源请求错误

先来看下几种状况

# 捕抓

## page(页面)中的JS报错

故意在页面js中写`abc()`这个不存在的方法

```javascript
page.onError = function(msg, trace) {

  var msgStack = ['PAGE ERROR: ' + msg];

  if (trace && trace.length) {
    msgStack.push('TRACE:');
    trace.forEach(function(t) {
      msgStack.push(' -> ' + t.file + ': ' + t.line + (t.function ? ' (in function "' + t.function +'")' : ''));
    });
  }

  console.log(msgStack.join('\n'));

};
```

输出

```javascript
PAGE ERROR: ReferenceError: Can't find variable: abc
TRACE:
 -> phantomjs://code/similar_picture.js: 1 (in function "global code")
 -> : 0 (in function "injectJs")
 -> phantomjs://code/report.js: 116
 -> phantomjs://platform/webpage.js: 286 (in function "_onPageOpenFinished")
```

## phantomjs 执行的js错误捕抓

```javascript
phantom.onError = function(msg, trace) {
  var msgStack = ['PHANTOM ERROR: ' + msg];
  if (trace && trace.length) {
    msgStack.push('TRACE:');
    trace.forEach(function(t) {
      msgStack.push(' -> ' + (t.file || t.sourceURL) + ': ' + t.line + (t.function ? ' (in function ' + t.function +')' : ''));
    });
  }
  console.log(msgStack.join('\n'));
  phantom.exit(1);
};
```

输出

```javascript
PHANTOM ERROR: ReferenceError: Can't find variable: abc
TRACE:
 -> phantomjs://code/report.js: 54 (in function global code)
```

## page中的资源请求错误

```javascript
page.onResourceError = function(){
    console.log('page.onResourceError'+ JSON.stringify(arguments));
}
```

请求一个域名不存在的接口

```bash
page.onResourceError{"0":{"errorCode":5,"errorString":"Operation canceled","id":42,"status":null,"statusText":null,"url":"http://127.0.1.1:10220/"}}
```

# phantom.onerror 对page.open不捕抓

假如

```javascript
page.open(address, function (status) {
  abc();//不存在的方法
}
```
phantom.onerror 是没有捕抓到,这时我只能靠自己的try,catch,进行判断,而且是程序卡主了不会往下执行,


# 总结

如果不进行捕抓,phantomjs将会卡在那里,不做任何处理或者退出程序
