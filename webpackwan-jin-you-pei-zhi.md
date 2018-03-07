# webpack万金油配置

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

IE8中首先不支持`Object.defineProperty`这种用法, 然后js如果给`Object.defineProperty`这个赋值进行polyfill

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



# 参考链接

1. hash和contenthash的说明: http://www.cnblogs.com/ihardcoder/p/5623411.html
2. babel的loose模式: http://2ality.com/2015/12/babel6-loose-mode.html
3. ECMAScript的支持列表: http://kangax.github.io/compat-table/es5/