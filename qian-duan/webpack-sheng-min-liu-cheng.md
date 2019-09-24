# Webpack生命流程

# 简单理解webpack配置

```javascript
const config = {
    entry: {},
    output: {},
    module: {
        rules: [
            {
                test: /\.js/,
                loader: 'babel-loader'
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin(options)
    ]
}
```

大致就是这样, 下面主要讲述下loader和plugins的作用

# loader作用

loader主要的作用是 进行源代码的转换

输入是源代码 输出是经过loader处理的代码

# plugins的作用

webpack其实只是一个空的插件机制系统

插件通过提供钩子来将不同的事件运行在不同的生命周期钩子当中来提供具体功能

# Tapable机制

庞大的webpack 其实主要入口文件 只是加载各种plugins而已

关于Tapable的详细说明 可以看这个文档 https://github.com/webpack/tapable

这里列举常用的API来方便大家阅读webpack源码

首先Tapable 是提供了发布订阅设计模式的API来实现hook机制

看下tapable提供了哪些hook 其区别是什么

```javascript
const {
	SyncHook,
	SyncBailHook,
	SyncWaterfallHook,
	AsyncParallelHook,
	AsyncParallelBailHook,
	AsyncSeriesHook,
	AsyncSeriesBailHook,
	AsyncSeriesWaterfallHook
 } = require("tapable");
```

说明一下钩子的组成单词部分

同步或异步的hook

- asnyc 异步任务
- sync 同步任务

异步任务中的并行或串行的hook

- paralle: 并行hook
- series: 并行hook

hook返回值的作用

- basic(默认): 单纯的调用每个订阅者
- waterfall: 调用每个订阅者, 并且把上一个订阅者的返回传递给下一个订阅者
- bail: 当订阅者有返回值时, 不继续执行后面的订阅者

如何使用

新建发布者

```javascript
class Car {
	constructor() {
		this.hooks = {
			accelerate: new SyncHook(["newSpeed"]),
			brake: new SyncHook(),
			calculateRoutes: new AsyncParallelHook(["source", "target", "routesList"])
		};
	}
}
```

hook的参数为发布者发布时, 对每个参数值的说明, 仅起到提示作用

订阅者订阅发布者

```javascript
const myCar = new Car();

// Use the tap method to add a consument
myCar.hooks.accelerate.tap("LoggerPlugin", newSpeed => console.log(`Accelerating to ${newSpeed}`));
```

第一个参数纯属写个名字表明 订阅的发布者/说明订阅的理由

上面有说到 hook分为sync/async

但同步和异步的hook 会触发的的订阅API不用

例如 sync的hook 只可以发布到 myHook.tap(...)中

而 async的hook 可以发布到 myHook.tap(), myHook.tapAsync, myHook.tapPromise()中

而hook选择发送到tap, tapAsync tapPromise是由发布者选择发布的API所决定的

```javascript
myHook.call() -> myHook.tap()

myHook.callSync() -> myHook.tapAsync() // 这个tapAsync 最后的一个参数为Tapable所增加进去的callback函数

myHook.promise() -> myHook.tapPromise()
```

以上就是 发布和订阅的API

但是Tapable 为钩子增加了钩子

```javascript
myCar.hooks.calculateRoutes.intercept({
	call: (source, target, routesList) => {
		console.log("hook通过call方法发布啦");
	},
	register: (tapInfo) => {
		// tapInfo = { type: "promise", name: "GoogleMapsPlugin", fn: ... }
		console.log(`${tapInfo.name} is doing its job`);
		return tapInfo; // may return a new tapInfo object
	}
})
```

# 简述Webpack生命流程

Webpack的生命流程由lodash/plugins的配置所决定

任何plugins都可以在Webpack生命流程中`插上一腿`

所以这里主要大致讲述下最重要的生命流程, 太细节的hook会忽略

```bash
-> 传入options并校验
-> 创建全局唯一生命流程compiler对象
-> 调用options.plugins数组插件函数
-> 执行compiler.run真正跑程序
-> new 构建的Compilation
-> 通知addEvtry钩子加载entry的资源
-> compilation进行buildModule
-> compilation进行shouldGenerateChunkAssets
-> compilation.emit完成
```

更详细可以看下网上的一张图 https://img.alicdn.com/tps/TB1GVGFNXXXXXaTapXXXXXXXXXX-4436-4244.jpg 引自 http://taobaofed.org/blog/2016/09/09/webpack-flow/

# Loader的API

官网详细文档: https://webpack.js.org/api/loaders/

首先Loader的基本结构

```javascript
export function pitch(requeremainingRequest, precedingRequest, datast) {
    // 这里this的数据结构 可参考 https://webpack.js.org/api/loaders/
    return '转换的内容'
}
export default function(content){
    // content 为文件的内容
    return '转换的内容'
}
```

> 多个loader执行顺序 

```javascript
module.exports = {
  //...
  module: {
    rules: [
      {
        //...
        use: [
          'a-loader',
          'b-loader',
          'c-loader'
        ]
      }
    ]
  }
};
```

最终的执行顺序

```javascript
|- a-loader `pitch`
  |- b-loader `pitch`
    |- c-loader `pitch`
      |- requested module is picked up as a dependency
    |- c-loader normal execution
  |- b-loader normal execution
|- a-loader normal execution
```

# Plugin常用的Webpack的hook

具体也可参考官网文档对plugin钩子的说明 https://webpack.js.org/api/compiler-hooks/

### Compiler相关hook

> Compiler.hooks.thisCompilation.tap('xxx', compilation => {})

在初始化compilation时触发, 也可以说在编译之前触发

> Compiler.hooks.make.tapAsync('xxx', (compilation, callback) => {})

在完成编译之前执行

> Compiler.hooks.emit.tapAsync('xxx', (compilation, callback) => {})

在输出静态文件之前执行

### Compilation相关hook

> compilation.hooks.buildModule.tap('xxx', module => {})

开始构建module之前触发

> compilation.hooks.optimize.tap('xxx', () => {})

在用optimize优化之前触发的钩子

> compilation.hooks.shouldGenerateChunkAssets.tap('xxx', () => {})

在生成chunks资源时触发

> compilation.hooks.childCompiler.tap('xxxx', (childCompiler, comilerName, compilerIndex) => {})

在childCompiler配置完毕之后执行

> compilation.hooks.normalModuleloader.tap('xxx', (loaderContext, module) => {})

在compilation中每个loader加载完毕都会触发这个钩子

tips: 这个钩子 文档说在 Webpack5.0里移除

# 如何阅读Webpack插件源码

Webpack体系巨大, 我们很难一开始就把Webpack整个体系的插件就阅读理解完毕

其实每个插件都是利用Webpack体系中各其他插件提供的hook

我们在阅读某一插件源码时, 就是需要了解插件源码中

- 依赖hook的执行时机
- hook回调所提供的参数
- hook回调方法中依赖方返回值的作用

例如WebAssemblyModulsPlugins需要把 是Initial的webassembly的chunks放入compilation的erros数组中

```javascript
compilation.hooks.afterChunks.tap("WebAssemblyModulesPlugin", () => {
    const initialWasmModules = new Set();
    for (const chunk of compilation.chunks) {
        if (chunk.canBeInitial()) {
            for (const module of chunk.modulesIterable) {
                if (module.type.startsWith("webassembly")) {
                    initialWasmModules.add(module);
                }
            }
        }
    }
    for (const module of initialWasmModules) {
        compilation.errors.push(
            new WebAssemblyInInitialChunkError(
                module,
                compilation.requestShortener
            )
        );
    }
});
```

这里就用到了afterChunks这个hook

而这个hook搜索webpack源码得知是 Compliation类中所提供的, 假设WebAssemblyModulsPlugins需要在

chunks前做一些处理, 也可以得知用beforeChunks对Webpack进行一些修改

```javascript
class Compilation extends Tapable {
    constructor(compiler) {
        super();
		this.hooks = {
            ...
            /** @type {SyncHook} */
			beforeChunks: new SyncHook([]),
			/** @type {SyncHook<Chunk[]>} */
			afterChunks: new SyncHook(["chunks"]),
        }
    })

    seal(callback){
        ...
        this.hooks.beforeChunks.call()
        ...
        this.hooks.afterChunks.call(this.chunks)
    }
}
```


# 阅读常用的Plugins源码

### html-webpack-plugin源码阅读

https://github.com/jantimon/html-webpack-plugin

代码主体结构

```javascript
function HtmlWebpackPlugin(options){
    this.options = _.extend(默认配置, options)
}

HtmlWebpackPlugins.prototype.apply = function(compiler){
    ...
    compiler.plugin('make', function (compilation, callback) {
        // 用当前compliation.createChildCompiler 创建子compiler 去处理html模板
    })

    compiler.plugin('emit', function(compilation, callback){
        // 筛选出new HtmlWebpackPlugin 时的配置chunks
        // 构建最终html 并 暴露 html生成流程的hook
    })
}

module.exports = HtmlWebpackPlugin
```

childCompiler主体实现

```javascript
module.exports.compileTemplate = function compileTemplate (template, context, outputFilename, compilation) {
    // 加载编译HTML模板需要的Plugin
    // childCompiler.runAsChild 真正执行childCompiler
    // 把html丢到comilation里到给loader处理(一般配置都为html-loader)
})
```

# 阅读常用的loader源码

### html-loader

https://github.com/webpack-contrib/html-loader

主体结构

```javascript
module.exports = function(content){
    // 通过 fastparse 库 将设置提取的资源保存在数组中
    // 将资源的位置转换为require('xxx')
    // 将html 用组件的方式暴露出去 利于module.exports = 'xxx'
}
```

### mini-css-extract-plugin.Loader

```javascript
export function pitch(request){
    // 验证参数
    // 创建ChildCompiler
    const childCompiler = this._compilation.createChildCompiler(
        `${pluginName} ${request}`,
        outputOptions
    );

    // 给childCompiler加载Plugin
    new NodeTemplatePlugin(outputOptions).apply(childCompiler);
    new LibraryTemplatePlugin(null, 'commonjs2').apply(childCompiler);
    new NodeTargetPlugin().apply(childCompiler);
    new SingleEntryPlugin(this.context, `!!${request}`, pluginName).apply(
        childCompiler
    );
    new LimitChunkCountPlugin({ maxChunks: 1 }).apply(childCompiler);

    // 转换请求loader的loader
    childCompiler.hooks.thisCompilation.tap(
        `${pluginName} loader`,
        (compilation) => {
        compilation.hooks.normalModuleLoader.tap(
            `${pluginName} loader`,
            (loaderContext, module) => {
            // eslint-disable-next-line no-param-reassign
            loaderContext.emitFile = this.emitFile;

            if (module.request === request) {
                // eslint-disable-next-line no-param-reassign
                module.loaders = loaders.map((loader) => {
                return {
                    loader: loader.path,
                    options: loader.options,
                    ident: loader.ident,
                };
                });
            }
            }
        );
        }
    );

}
```

# 参考文章

- webpack的生命流程和钩子: https://juejin.im/post/5bb06c55e51d450e7c0d8ab9

