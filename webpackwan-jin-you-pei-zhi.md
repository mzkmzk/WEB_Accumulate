# webpack万金油配置

# hash chunkhash contenthash的区分

> hash

compilation的hash值

可以简单理解为 每执行一次打包 都会有一次compilation的hash值 

compilation对象代表某个版本的资源对应的编译进程

tips:

在file-loader的配置中, hash代表的是该文件的hash

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

# 参考链接

1. hash和contenthash的说明: http://www.cnblogs.com/ihardcoder/p/5623411.html