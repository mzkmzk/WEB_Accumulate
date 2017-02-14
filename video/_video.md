# 基础知识

# 事件流程

pc android ios 的事件流程不太一致,在这里列出主要的

1. loadstart: 视频开始加载
2. canplaythrough: 可以流畅播放
3. playing: 开始播放(android兼容性可能不好)
4. timeupdate: 进度发生变化(可以在这里获取currentTime,检查到变化则肯定为播放了)
5. error: iOS有明确错误,android则可能有莫名错误
6. stalled: 网络状况差,导致异常网络状况不佳，导致视频下载中断

# video属性

1. poster: 封面图片
2. preload: 预加载 iOS不支持,android支持
3. autoplay: 自动播放 iOS不支持
4. loop: 循环播放
5. controls: 控制条
6. width,height: 一定要给出并且不能为0,android下css设置的可能会无效

# 其他浏览器属性

1. webkit-playsinline&&playsinline: iOS加上这个则为内联播放(应该只支持iOS10以上),两个属性最好都加上
2.  x-webkit-airplay: 值为`allow|true`时,支持投影到其他AirPlay上
3. x5-video-player-type: 设为'h5',意义为x5内核下,使用同层H5播放器
4. x5-video-player-fullscreen: 为false时,阻止x5全屏

# 禁止全屏播放问题

默认手机端,基本都会全屏播放

但我们有时候需要内联!

```html
<video  webkit-playsinline="" playsinline="" x-webkit-airplay="allow" x5-video-player-type="h5" x5-video-player-fullscreen="false">
```



# 参考资料

1. 挺全的video资料: http://zhaoda.net/2014/10/30/html5-video-optimization/
2. 基本语法介绍: http://know.webhek.com/html5/html5-video-audio.html
3. 各终端video的研究: https://yq.aliyun.com/articles/4268
4. 各属性实时展示: https://www.w3.org/2010/05/video/mediaevents.html