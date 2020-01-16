# webpack性能优化经验

# webpack打包后文件过大

### 分析包体积过大

这一步 其实主要查看引用了哪些重复的包, 或者不必要的包

分析包大小由哪些小包构成 这个主要用到的是

[webpack-bundle-analyzer](https://github.com/webpack-contrib/webpack-bundle-analyzer)

使用很简单 看README即可



下面分析一次我个人的优化包大小经验

刚录完代码 格式大小为 stat size: 1.07 MB, parsed size: 1.46MB: gzip size 251.4kb

>  子组件和分组件多次对同一scss

![scoped重复渲染](/Users/maizhikun/GitBook/Library/mzkmzk/WEB_Accumulate/assets/QQ20191223-125138.jpg)

 stat size: 851.61KB, parsed size: 1.2MB: gzip size 212.11kb

> 不重复引用第三方库

我的这个组件 是直接提供到CDN处让业务使用, 业务本身会引VUE的CDN, 所以window.Vue就是Vue

所以打包的时候引用Vue则引用window.Vue

webpack设置



```javascript
externals: {
    vue: 'Vue'
  }
```

 stat size: 616.23KB, parsed size: 984.95KB: gzip size 147.63kb



> @babel/preset-env和@babel/plugin-transform-runtime同时使用

这里需要理解下这两个包的原理, 其实都是解决浏览器兼容性问题

现只保留@babel/preset-env



 stat size: 584.32KB, parsed size:857.38KB: gzip size 131.03kb

