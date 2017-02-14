# canvas播放视频

# 背景

android里有些机型无法阻止全屏播放,或者即使去内嵌播放也会有问题

所以考虑有canvas解决这个问题

视频是没有声音的视频,不然我可能不会考虑canvas.不然估计还得用上audio会更麻烦

# 简单实现

```javascript
var v=document.getElementById("video");
var c=document.getElementById("my_canvas");
var ctx=c.getContext('2d');
v.addEventListener('play',function() { 
  var i = window.setInterval(function() {
    ctx.drawImage(v, 0, 0, 270, 135);
  }, 500);
  v.addEventListener('pause',function() {window.clearInterval(i);},false);
  v.addEventListener('ended',function() {clearInterval(i);},false);
},false);
```

有一个插件专门解决这个问题,可惜,android也是显示黑屏的

他的demo地址: https://stanko.github.io/html-canvas-video-player/,其中的视频就是canvas播放,但是android下不行

# 问题

iOS没问题,但是在android微信里是黑屏

userAgent:

```shell
Mozilla/5.0 (Linux; Android 6.0; PRO 6 Build/MRA58K; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/53.0.2785.49 Mobile MQQBrowser/6.2 TBS/043024 Safari/537.36
```