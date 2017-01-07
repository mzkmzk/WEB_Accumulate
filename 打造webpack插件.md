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


# Compiler and Compilation

## Compiler

Compiler对象是webpack环境的配置对象

生命周期

1. run(compiler: Compiler) async
2. watch-run(watching: Watching) async
3. compilation(c: Compilation, params: Object)
4. context-module-factory(cmf: ContextModuleFactory)
5. compile(params)
6. make(c: Compilation) parallel
7. after-compile(c: Compilation) async
8. emit(c: Compilation) async
9. after-emit(c: Compilation) async
10. done(stats: Stats)
11. failed(err: Error)
12. invalid()
13. after-plugins()
14. after-resolvers()

其实前两个个一般是webpack处理,后面的才会有钩子

引用方法

```javascript
compiler.plugin("compilation", function(compilation) {
    //you are now in the "compilation" phase
});
```

## Compilation

compilation是创建assets的,并给大家提供了很多钩子,在中途可以改变assets

生命周期

# 参考链接

1. https://webpack.github.io/docs/how-to-write-a-plugin.html
2. https://webpack.github.io/docs/plugins.html#the-compiler-instance
