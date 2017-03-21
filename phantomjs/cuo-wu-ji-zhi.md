# 捕抓错误

# 背景

捕抓错误一般是

phantom.onError和pageonError

先来看下几种状况

# 实验

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
