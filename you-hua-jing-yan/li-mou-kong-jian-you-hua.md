# 离某空间优化

# 背景

项目运用了react全家桶

# 目标

优化前非压缩包1.64M, 目标为优化其包体积

# jquery转zepto

由于jquery主要使用了ajax, 没有使用callbacks之类的东西 所以可以转为zepto

但是zepto直接运用在react会有报错

`Cannot read property 'createElement' of undefined`

解决方案为

`npm install --save-dev imports-loader`

然后修改webpack.config

```javascript
loaders: [
             {
                test: /\/node_modules\/zepto\/dist\/zepto\.js$/,
                loader: "imports-loader?this=>window"
            },
            ...
         ]
```

1.64M -> 1.43 MB 减少了`210KB`

这里还可以`webpack --display-modules --sort-modules-by size`看下所有被打包的模块 有没有自己不用的 我自己的项目现在是木有

# 检查babelrc

```javascript
{
	"presets": [
		"es2015",
		"react"
	],
    "plugins": [
        "transform-object-assign",
        "react-hot-loader/babel"
    ]
}

```
把react-hot-loader/babel去掉...

1.43M -> 1.40 MB 减少了`30KB`

# 打通用组件包

这里其实不能减少包大小

只能将一些第三库 打成一个vendor.js 做 长久缓存

```javascript
entry:{ 
        index : [
        //'webpack-dev-server/client?http://localhost:8080',
        //'webpack/hot/only-dev-server',
        './Src/View/Index/index'],
        vendor: ['zepto','react','react-dom','react-redux','redux','redux-thunk','underscore','k-logging', 'react-router']
    },
 plugins: [
    new webpack.optimize.CommonsChunkPlugin({
            name: "vendor",
            filename: "vendor.js",
            chunks: ['index'],
        }),
 ]
```

# 升级到webpack3

这个特性的教程都是在webpack2上的 所以要先把我的webpack1干脆升级到3

1. 首先要把package和webpack相关的包 都重新 npm install 升级到最新的版本
2. 改变extract-text-webpack-plugin的打包css改为` {test: /\.css$/,loader: ExtractTextPlugin.extract({fallback: "style-loader",use: "css-loader"})`

index.bundle.js 从 291kb -> 285kb

vendor.js 从1.02M -> 1000KB

index.css 69.4kb 没变

# 总结

最终 减少包大小240kb......

index.bundle大小为291KB 打包后106KB

verndor.js为1.02M 打包后 打包后 280kb
