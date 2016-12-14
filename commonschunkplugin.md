#CommonsChunkPlugin

一般的SPA项目打包起来如果都如果都放在一个js里,可能有4,500k,第三方包例如react就有300k,我们自己的代码只有100k左右,如果每次更新都把单独的一个js更新掉,那样有点浪费

所以建议把js文件分成两个

1. common.js: 第三方库,几百年都不变动的 
2. index.js: 自己的代码,经常会更新的


#DEMO

```javascript
var config = {
	entry:{ 
        index : [
        './Src/View/Index/index'],
        common: [
            'react','react-dom','jquery','redux','redux-thunk','react-redux','underscore','k-logging','k-report'
        ]
    },
    ...
     plugins: [
        new webpack.optimize.CommonsChunkPlugin({
            name: "common", //名字会被filename覆盖
            filename: "common.js",
            chunks: ['index'],
        }),
    ]
```

结果
```shell
//未压缩
common.js    1.11 MB       0  [emitted]  common
index.bundle.js     316 kB       1  [emitted]  index
```

在entry中的common里写需要写上第三方的库

注意要在chunks上写上包含此common的入口文件



