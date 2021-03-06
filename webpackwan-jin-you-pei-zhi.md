# webpack万金油配置

# 提取JS的公共部分

例如我们的公共库 jquery、underscore等

```javascript
entry:{
    'vendor': ['jquery', 'underscore']
},
plugins: [
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor', // 将公共模块提取，生成名为`vendor`的chunk
    })
]
```

# css文件处理

css文件可以内联在js 或者 内联在html里, 不过我习惯是把css单独成一个文件

```js
module: {
    rules: [
        {
            test: /\.css$/,
            use: ExtractTextPlugin.extract({
                fallback: 'style-loader',
                use:  'css-loader' 
            })
        },
        {
            test: /\.(png|jpg|gif)$/,
            use: [{
                loader: 'file-loader?name=[name]',
                options: {
                    limit: 1,
                    name: '[name].[ext]?h=[hash:3]'
                }
            }]
        }
    ]
},
plugins: [
     new ExtractTextPlugin( (__DEV__ ? '[name].css' : '[name].min.css') + "?h=[contenthash:3]"),
]
```

fallback 当css-loader无法处理时, 就交给style-loader处理

extract-text-webpack-plugin README: https://github.com/webpack-contrib/extract-text-webpack-plugin

# 多页面的HTML部分

```javascript
plugins: [
    new HtmlWebpackPlugin({  // Also generate a test.html
      title: '首页title',
      filename: 'index.html',
      template: 'src/assets/index.html'
    },
    new HtmlWebpackPlugin({  // Also generate a test.html
      filename: 'test.html',
      template: 'src/assets/test.html'
    })
  ]
```

html-webpack-plugin官方文档: https://github.com/jantimon/html-webpack-plugin#configuration

文档中列举了很多基于这个插件开发出来更多的插件

假如需要对输出的html做处理, 也可以做个插件来简化平时的html操作

# hash chunkhash contenthash的区分

> hash

compilation的hash值

可以简单理解为 每发生一次变化后打包 都会有一次compilation的hash值 

一次变化可以是

1. js等模块的变化
2. webpack.config.js的配置内容的变化

compilation对象代表某个版本的资源对应的编译进程

tips:

在file-loader的配置中, hash代表的是该文件的hash

在`html-webpack-plugin`插件中的属性hash:true 表示的也是 compilation的hash值

> chunkhash

在webpack的配置.entry对象中每个模块

而chunkhash就根据js模块的内容 来计算hash值

> contenthash

contenthash只有 `extract-text-webpack-plugin` 中使用

例如

```javascript
plugins: [
    new ExtractTextPlugin("styles.css?hash=[contenthash:3]") 
]
```

它只表示自己文件的hash

> 建议配置

```javascript
{
    output: {
        filename: [name].js?hash=[chunkash]
    },
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ExtractTextPlugin.extract({
                    fallback: 'style-loader',
                    use:  'css-loader' 
                })
            },
            {
                test: /\.(png|jpg|gif)$/,
                use: [{
                    loader: 'file-loader?name=[name]',
                    options: {
                        limit: 1,
                        name: '[name].[ext]?hash=[hash:3]'
                    }
                }]
            }
        ]
    },
    plugins: [
        new ExtractTextPlugin("styles.css?hash=[contenthash:3]")
    ]
}
```

以上的配置基本可以满足 

1. js只会根据自己的js模块代码变化而变化 
2. 图片的url只会根据自身文件的hash变化而变化
3. styles.css中只因为css代码变化而变化

但是分析下面几种情况

前提: index.js中import了index.css, index.css中引用了index.png

1. 那么假如index.css内容变化了, index.js的版本号会变化吗? 不会
2. 假如index.png内容变好了了, index.js和index.css的版本号会变化吗? index.js不变 index.css变

# 兼容性问题

> default catch关键字

跟catch关键字冲突的情况不多, 而default在IE8报错里基本很常见

因为 `export default ...`这样的话, 编译后是这样的

编译前
```javascript
import utils from './utils'

export default index 
```

编译后

```javascript
var _utils = __webpack_require__(4);

var _utils2 = _interopRequireDefault(_utils);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; } //可以用 transform-es3-property-literals解决 

exports.default = index //可以用 transform-es3-member-expression-literals 解决 
```

上面的default 都会引起报错 而解决

1. [transform-es3-property-literals](https://babeljs.io/docs/plugins/transform-es3-property-literals/ )会把 `{ default: obj }` 变为 `{ 'default': obj }`
2. [transform-es3-member-expression-literals](https://babeljs.io/docs/plugins/transform-es3-member-expression-literals/)会把 `exprts.default`变为  `exrpots['default']`

这两个一般在.babelrc中添加

```bash
{
  "presets":  [
    ["env", {
      "targets": {
        "browsers": ["ie >= 7"]
      },
      "loose": true
    }]
  ],
  "plugins": [
    "transform-es3-property-literals", //重点
    "transform-es3-member-expression-literals" //重点
  ]
}
```

解决这个问题的插件还有`es3ify-loader`

tips: 

后面提到的ugilyjs也可以解决这类问题

> class 导致的Object

源码

```javascript
class Ball {
    hide_all(){
       
    }
}
```

正常的 normal 模式 会把这个转变为 

```javascript
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

 _createClass(Ball, [{
    key: 'hide_all',
    value: function hide_all() {}
}, { ... }
```

这里的`Object.defineProperty`是IE8的终极噩耗...

IE8中

1. 自己实现的`Object.defineProperty`和标准不一样, 只能接受DOM对象
2. js如果给`Object.defineProperty`这个赋值进行polyfill

IE8也会报错 

只能将转换模式转为loose

编译后为 

```javascript
function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

function Ball(options) {
    _classCallCheck(this, Ball);

    this.options = options;
}

Ball.prototype.hide_all = function hide_all() {};
```

在babekrc中添加

```bash
{
  "presets":  [
    ["env", {
      "targets": {
        "browsers": ["ie >= 7"]
      },
      "loose": true //重点
    }]
  ],
  "plugins": [
    "transform-es3-property-literals", 
    "transform-es3-member-expression-literals" 
  ]
}
```

更多loose模式和normal模式的区别可参加<http://2ality.com/2015/12/babel6-loose-mode.html>

> UglifyJS的作用

tips: 

webpack v3 自带的uglifyjs-webpack-plugin版本是0.46 

对应的文档是 https://github.com/webpack-contrib/uglifyjs-webpack-plugin/tree/v0.4.6

而 uglifyjs-webpack-plugin自带的ugilyfyjs是2.8.29

对应的文档是: https://github.com/mishoo/UglifyJS2/tree/v2.x

uglifyjs 理论上只做压缩和混淆代码的作用 

但实际上 还可以帮忙处理IE8的问题 其中就有上面的default和catch关键字等

这里有个小插曲

webpack实现的动态require.ensure时

业务代码中使用

```javascript
require.ensure(['./mock_data.js'], function(require) {
    ...
}, 'mock_data');
```

在编码后会变成

```javascript
 __webpack_require__.e/* require.ensure */(0).then((function (require) {
    ...
}).bind(null, __webpack_require__)).catch(__webpack_require__.oe);
```

编译后 这个catch 虽然上面说的`transform-es3-member-expression-literals`会解决

但是注意 编译后的这个代码并不是我们业务代码写的, 而是webpack加的

所以babel并没有去帮这里的catch去转换

这时需要把uglifyjs的配置兼容下IE8

```bash
new webpack.optimize.UglifyJsPlugin({
    compress: {
        screw_ie8: false //主要是这里去给catch等关键字做兼容
    },
    output: {
        screw_ie8: false
    }
})
```

UglifyJs在文档里并没有找到这两个screw_ie8兼容IE8的属性

也没有说明为支持IE8的做了什么处理(看源码也没咋懂)

只能从别人的博客里找到 或者在源码中 发现文档里没列明的属性

例如 

1. compress: https://github.com/mishoo/UglifyJS2/blob/v2.x/lib/compress.js
2. output: https://github.com/mishoo/UglifyJS2/blob/v2.x/lib/output.js

compress在文档里属性并没有代码中的全: http://lisperator.net/uglifyjs/compress


再tips: 

本来想在webpack用uglifyjs-webpack-plugin而直接试用UglifyJS3的 

因为uglify-js的文档感觉是比较全的

但是折腾来折腾去 总是没压缩

之后就没弄了 乖乖弄webpack自带的uglify

> 编码后 webpackBootstrap 还有Object.defineProperty

如果用了 webpack就会用webpackJsonpCallback来实现动态加载js

其中有段代码 令人起毛

```javascript

/******/ 	// define getter function for harmony exports
/******/ 	__webpack_require__.d = function(exports, name, getter) {
/******/ 		if(!__webpack_require__.o(exports, name)) {
/******/ 			Object.defineProperty(exports, name, {
/******/ 				configurable: false,
/******/ 				enumerable: true,
/******/ 				get: getter
/******/ 			});
/******/ 		}
/******/ 	};
```

但看下注释是 当harmony模式exports采用运行这个方法 而我们的babelrc设置的是 `browsers: ["ie >=7"]`

会默认不采取harmony取exports, 所以不会进入到这个方法 所以没影响

> babel-polyfill 还是 transform-runtime

首先来介绍下两者的作用

polyfill 

其实比较好理解

就是在全局中 添加新语法 例如 Promise, Array.prototype.includes

tranform-runtime

的作用有两个

一: 减少内部实现的次数

看个例子

源代码:

```javascript
class Person {
}

```    

babel的默认转换方式是

```javascript
"use strict";

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

var Person = function Person() {
  _classCallCheck(this, Person);
};
```

但是问题就在于假如再有个 class Student , class Teacher

babel会在每个类都实现一次 _classCallCheck

而babel-runtime 就会把 _classCallCheck的实现放在一个babel-runtime的模块里

供其他需要用的代码, 直接require即可

babel-runtime后的代码

```javascript
"use strict";

var _classCallCheck2 = require("babel-runtime/helpers/classCallCheck");

var _classCallCheck3 = _interopRequireDefault(_classCallCheck2);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

var Person = function Person() {
  (0, _classCallCheck3.default)(this, Person);
};
```

二: 新语法的兼容

这里说的是 Promise Set Map等语法

但是runtime不是用全局的方法是实现的, 而且require到自己的库, 然后实现的

当然上面说的这些runtime都可以配置

```bash
{
  "plugins": [
    ["transform-runtime", {
      "helpers": true, // 默认都引入带runtime的库 而不多次实现 
      "polyfill": true, //是否帮忙转义Promise 等语法
      "regenerator": true, // regenerator 的语法是否兼容
      "moduleName": "babel-runtime"
    }]
  ]
}
```

总结

runtime最大的优点和缺点都在于不会去污染全局代码

缺点在哪? 

例如 `"foobar".includes("foo")` 这样的语法没办法兼容的

因为要兼容, 必须重写 String.prototype.includes, 而runtime不会这么做

所以在Array String 等一些拓展函数, runtime不会帮助到你

所以

runtime比较适合工具库的使用, 因为工具库不应该污染全局, 但是runtime的helpers属性 也许也能帮助减少代码的最终大小

但是 polyfill最大的问题就是太大了 

所以最终的建议是

在进行业务开发时

在<https://github.com/zloirock/core-js> 里选择性的 polyfill+runtime

插件开发 其实比较建议就用原生的JS来开发, 减少依赖, 减少插件包的大小





# 参考链接

1. hash和contenthash的说明: http://www.cnblogs.com/ihardcoder/p/5623411.html
2. babel的loose模式: http://2ality.com/2015/12/babel6-loose-mode.html
3. ECMAScript的支持列表: http://kangax.github.io/compat-table/es5/
4. 更多关于preset-env的配置: https://babeljs.io/docs/plugins/preset-env/#loose
5. 常见的IE8兼容性坑: http://www.aliued.com/?p=3240
6. 解决IE8常见的坑: https://github.com/zuojj/fedlab/issues/5
7. babel-trantime文档: https://babeljs.io/docs/plugins/transform-runtime/#helper-aliasing
8. babel-polyfill文档: https://babeljs.cn/docs/usage/polyfill/

