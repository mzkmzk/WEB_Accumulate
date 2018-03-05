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

> 小测试 

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
