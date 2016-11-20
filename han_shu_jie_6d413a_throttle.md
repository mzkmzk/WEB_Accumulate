# 函数节流: throttle

# 作用

一般处理onresize和onScroll这种事件监听

触发频率是相当高德,所以我们最好做下节流

# 输入

`_.throttle(function, wait, [options]) `

1. func: 准备被节流的函数
2. wait: 至少每隔wait毫秒执行一次func
3. options: 

    1. leading: false 禁用第一次的执行,默认true
    2. trailing: false 禁止最后一次的执行,默认true


# 源码

```javascript
 _.throttle = function(func, wait, options) {
    var context, args, result;
    var timeout = null;
    var previous = 0;
    if (!options) options = {};
    var later = function() {
      previous = options.leading === false ? 0 : _.now();
      timeout = null;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    };
    return function() {
      var now = _.now();
      if (!previous && options.leading === false) previous = now;
      var remaining = wait - (now - previous);
      context = this;
      args = arguments;
      if (remaining <= 0 || remaining > wait) {
        if (timeout) {
          clearTimeout(timeout);
          timeout = null;
        }
        previous = now;
        result = func.apply(context, args);
        if (!timeout) context = args = null; //这里其实不是太明白,上面的if已经把timeout弄成null了,这里应该是永真的啊?.
      } else if (!timeout && options.trailing !== false) {
        timeout = setTimeout(later, remaining);
      }
      return result;
    };
  };
```