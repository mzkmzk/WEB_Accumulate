# video标签

# 视频格式的兼容性

![视频兼容性](/assets/ccdb75581d846d294572296b3ef04a1b.png)

注意: 现在浏览器基本都支持mp4.包含在fixfox


# 事件流程

pc android ios 的事件流程不太一致,在这里列出主要的

1. loadstart: 视频开始加载
2. canplaythrough: 可以流畅播放
3. playing: 开始播放(android兼容性可能不好,ipad无触发)
4. timeupdate: 进度发生变化(可以在这里获取currentTime,检查到变化则肯定为播放了)
5. error: iOS有明确错误,android则可能有莫名错误
6. stalled: 网络状况差,导致异常网络状况不佳，导致视频下载中断
7. abort: 当替换src时会触发,其余未发现

# video属性

1. poster: 封面图片
2. preload: 预加载 iOS不支持,android支持
3. autoplay: 自动播放 iOS不支持
4. loop: 循环播放
5. controls: 控制条
6. width,height: 一定要给出并且不能为0,android下css设置的可能会无效
7. crossorigin: 设置anonymous跨域
8. buffered: 无什么用,因为浏览器的缓存估计根据每个站点来,最大的缓存为多少.所以每次播放时,都会拉去



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

# 其余

一个支撑flv在手机上播放的插件: https://github.com/Bilibili/flv.js

# 缓存问题

如果设置了loop,循环播放时,会重新执行一遍事件而且如果视频过大,则chrome不缓存

chrome版本: `"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36"`

我估计缓存了5M

因为每次循环,会重新请求1~2M,而且每次都不一定

# 参考资料

1. 挺全的video资料: http://zhaoda.net/2014/10/30/html5-video-optimization/
2. 基本语法介绍: http://know.webhek.com/html5/html5-video-audio.html
3. 各终端video的研究: https://yq.aliyun.com/articles/4268
4. 各属性实时展示: https://www.w3.org/2010/05/video/mediaevents.html
5. video事件列表: https://developer.mozilla.org/zh-CN/docs/Web/Guide/Events/Media_events