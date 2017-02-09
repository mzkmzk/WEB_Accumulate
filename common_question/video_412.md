# video 412错误

# 错误代码

```javascript
www.xx.com/:1 GET http://www.xxx.com/v2017/k_pc/public/video/video.mp4 412 (Precondition Failed)
2017-02-09 11:00:52.395 /v2017/k_pc/public/#:1 Uncaught (in promise) DOMException: Failed to load because no supported source was found.
2017-02-09 11:00:54.876 index.js?7cc64f7…:185 Uncaught (in promise) DOMException: The element has no supported sources.
```

# 412解释

一般是因为服务器的 If-Unmodified-Since 或 If-None-Match 未实现

# 解决方案

```javascript
media.addEventListener('error', function (e) {

   var date = new Date();
   var milliSecs = date.getMilliseconds();
   var curr_src = $(media[0]).attr('src');
   var curr_src_arr = curr_src.split("?");
   var new_src = curr_src_arr[0]+"?t="+milliSecs;

   $(media[0]).attr('src',new_src);
   $(media[0]).find('source').attr('src',new_src);
   media[0].load();
   //media[0].play(); /* Here we can not trigger play video/audio without user interaction. */
}, false);
```

# 参考链接

1. https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/412
2. http://stackoverflow.com/questions/41825792/how-to-fix-412-precondition-failed-error-html5-video-tag