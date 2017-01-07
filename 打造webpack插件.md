# 打造webpack插件

想要打造webpack插件

主要需要了解webpack给我们提供了什么钩子

钩子里面有什么函数或者数据我们是可以操作的

# 基本example

```javascript
function HelloWorldPlugin(options) {
  // Setup the plugin instance with options...
}

HelloWorldPlugin.prototype.apply = function(compiler) {
  compiler.plugin('done', function() {
    console.log('Hello World!'); 
  });
};

module.exports = HelloWorldPlugin;
```

在webpack.config.js

```javascript
var HelloWorldPlugin = require('hello-world');

var webpackConfig = {
  // ... config settings here ...
  plugins: [
    new HelloWorldPlugin({options: true})
  ]
};
```

# webpack打包流程

首先webpack的作用是?

1. 打包
2. 对文件进行特殊编译

其实是一个的输入和输出的过程

输入当然就是我们设置的webpack.config.js和源代码

而webpack就是通过其自身和插件,处理并帮助我们完成任务

webpack概念基于

1. chunks: 在webpack中你所定义的模块
2. assets 输出的文件

打包过程

1. make: w1
2. compiler.plugin('done', function(){})

# 参考链接

1. https://webpack.github.io/docs/how-to-write-a-plugin.html
2. https://webpack.github.io/docs/plugins.html#the-compiler-instance
