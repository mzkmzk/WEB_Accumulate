# 函数节流与反抖

做研究的demo地址: http://demo.404mzk.com/test/throttle_and_debounce/



# 节流

## underscore的throttle简介

为了指定时间内函数只被触发一次

`.`代表函数被触发时, `|`代表函数真正执行了, 这里显示每1秒触发一次函数`.`, 限定每3秒内只能触发一次

这里以underscore的throttle为例

`.|...|...|`

这种是最正常的情况

首先触发了一次`.`, 然后函数立马执行,1 然后中途多次触发`.`, 但是每秒才能触发一次

比较特殊的情况, 执行了2次`.`

按理来说, 只会触发一次函数, 但underscore实际触发了两次

`.|.|`

underscore的throttle有两个参数

1. leading: false;//这里如果设置了 会阻止第一个`|`被执行
2. trailing: false; //这里如果设置了 会阻止第二个`|`被执行

这种情况其实很常见, 如果没有设置上面两个参数, 并且假设`|`设置了ajax请求, 那么很容易连续触发两次ajax, 这是绝对不允许的

一般ajax的话, 建议`leading: false`

## underscore的throttle源码

```javascript
  _.throttle = function(func, wait, options) {
    var timeout, context, args, result;
    var previous = 0;
    if (!options) options = {};

    var later = function() {
      previous = options.leading === false ? 0 : _.now();
      timeout = null;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    };

    var throttled = function() {
      var now = _.now();
      if (!previous && options.leading === false) previous = now;
      var remaining = wait - (now - previous); // 代表还有多少时间, 可以执行下一次函数
      context = this;
      args = arguments;
      
      if (remaining <= 0 || remaining > wait) { //只有当没有设置options.leading = false和 非节流的情况第一次能进来这里. 
        if (timeout) { // 虽然 remaining不可能超过wait, 这里没必要clearTimeout(timeout), 因为如果setTimeout能准确执行的话, 这里timeout肯定不存在
          clearTimeout(timeout); //但是seTimeout 并不准确, 可能会延迟, 所以可能到了超过remaining的时间, 但setTimeout还没执行, 所以要移除掉
          timeout = null;
        }
        previous = now;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      } else if (!timeout && options.trailing !== false) {// 被节流的函数都要执行setimeout, 
        timeout = setTimeout(later, remaining);
      }
      return result;
    };

    throttled.cancel = function() {
      clearTimeout(timeout);
      previous = 0;
      timeout = context = args = null;
    };

    return throttled;
  };
```

## 简单做法

underscore的throttle虽然是功能强大, 但是我们有没有办法做出一个建议的节流呢

如果非通用的话 ,这里就是一个节流

```javascript
var my_throttled_timeout = null,
    prev_time = null,
    my_function = function(){
        document.querySelector('.j_my_throttle_sum').innerHTML = parseInt(document.querySelector('.j_my_throttle_sum').innerHTML) + 1;
        my_throttled_timeout = null
        prev_time = new Date().getTime();

    }
window.addEventListener('mousemove', function(){
    if (my_throttled_timeout) return;
    remaining = 3000 - ( new Date().getTime() - prev_time )
    if ( remaining <= 0 ) {
        my_function()
    }else {
        my_throttled_timeout = setTimeout(function(){
        my_function()
    }, remaining)
    }   
})
```

# 反抖

在某个连续触发的函数时, n秒内没有再被触发, 则执行一次

应用场景: 拖动window的size, 不断触发resize, 当停止拖动时, 才做处理

`................空空空|..............空空空|` 当n为3000时

## underscore的debounce

```javascript
  _.debounce = function(func, wait, immediate) {
    var timeout, result;

    var later = function(context, args) {
      timeout = null;
      if (args) result = func.apply(context, args); //只有immediate非true时 才会执行到这里
    };

    var debounced = restArgs(function(args) {
      if (timeout) clearTimeout(timeout);
      if (immediate) { //立即先执行一次 然后再进行反抖
        var callNow = !timeout;
        timeout = setTimeout(later, wait);
        if (callNow) result = func.apply(this, args);
      } else {
        timeout = _.delay(later, wait, this, args);
      }

      return result;
    });

    debounced.cancel = function() {
      clearTimeout(timeout);
      timeout = null;
    };

    return debounced;
  };
```

这个思路比较清晰, 就是不断触发就不断clearTimeout

而immediate是何作用

一般默认的debounce 是`.空空空|`(n为3000)

而如果想要`.|....空空空|`的效果如何, 这就是immediate, 如果之前n秒前没被执行过, 第一次触发 会立马执行,然后再进行防抖

# 参考链接

1. http://www.alloyteam.com/2012/11/javascript-throttle/
2. underscore的做法: http://www.qdfuns.com/notes/17398/93ec1a3c4cf2a1ecd5f8ff1a905f2d03