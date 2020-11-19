# PWA入门

# PWA的主要特点

- Installable: 应用可被安装
- Network independent: 在离线/网速很差的情况下运行
- Re-engageable: 无论何时都能发送通知
- Linkability: 可以通过链接连接到打开PWA应用

# 实现PWA的主要依赖技术点

### Web app manifest

> 实现

页面主要在根目录提供一个`manifest.json`文件

`manifest.json`主要提供

- start_url: 设置启动网址
- icons: 各分辨率下的图标

[详细的API文档](https://developer.mozilla.org/en-US/docs/Web/Manifest)

例如某国外知名社交应用的mainfest.json

```javascript
{
    "background_color": "#ffffff",
    "description": "It's what's happening. From breaking news and entertainment, sports and politics, to big events and everyday interests.",
    "display": "standalone",
    "gcm_sender_id": "49625052041",
    "gcm_user_visible_only": true,
    "icons": [],
    "name": "xxx",
    "share_target": {},
    "shortcuts": [],
    "short_name": "xxx",
    "start_url": "https://xxx.com/?utm_source=homescreen&utm_medium=shortcut",
    "theme_color": "#ffffff",
    "scope": "/",
    "android_package_name": "com.xxx.android",
    "prefer_related_applications": true,
    "related_applications": [
        {
            "id": "com.xxx.android",
            "platform": "chromeos_play",
            "url": "https://play.google.com/store/apps/details?id=com.xxx.android"
        }
    ]
}
```

> 浏览器兼容

android在近代系统都支持

ios则是在的`ios-safari 11.3`开始部分支持

[manifest浏览器兼容](https://caniuse.com/?search=Web%20app%20manifest)

### Service worker

> 实现

这里主要的工作是实现离线缓存和加快访问同一应用其他页面的速度

介绍下`Service worker`的主要生命流程

`注册、安装成功（安装失败）、激活、运行、销毁。`

安装时指定要缓存的代码

```javascript
 // 在sw.js中监听对应的安装事件，并预处理需要缓存的文件
 // 该部分内容涉及到cacheStorage API
 
 // 定义缓存空间名称
 const CACHE_NAME = 'sylvia_cache'
 
 // 定义需要缓存的文件目录
 let filesToCache = [
   '/src/css/test.css',
  '/src/js/test.js'
]
// 监听安装事件，返回installEvent对象
self.addEventListener('install', function(event){

  // waitUntil方法执行缓存方法
  event.waitUntil(

    // cacheStorage API 可直接用caches来替代
    // open方法创建/打开缓存空间，并会返回promise实例
    // then来接收返回的cache对象索引
    caches.open(CACHE_NAME).then(cache => {

      // cache对象addAll方法解析（同fetch）并缓存所有的文件
      cache.addAll(filesToCache)
    })
  )
})

```

缓存后 我们可以查看代码的缓存情况

![Cache Storage缓存文件](https://mzk-blog-static.oss-cn-shenzhen.aliyuncs.com/study-css-demo/QQ20201118-175005.png)

我的理解是 浏览器加载资源时 会先来`Cache Storage`来查有无文件, 这样来实现离线访问的功能

[具体的Service worker使用参考这里](https://www.infoq.cn/article/geVZkkhue8bki9PFvPxe)

> 浏览器兼容

除了IE和ios11.2及其之前的版本不兼容, 其他基本都支持

[查看Service work浏览器兼容情况](https://caniuse.com/?search=service%20work)

[查看CacheStorage浏览器兼容情况]()https://caniuse.com/?search=CacheStorage

# 参考链接

- https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps/Introduction