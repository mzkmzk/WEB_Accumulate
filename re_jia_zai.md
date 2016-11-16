# 热加载

# 用处

在打代码的时候,特别是在客户端打代码的时候,有时候缓存是很恶心的,热加载还是不错的

# how

1. npm install --save-dev react-hot-loade
2. npm install -g webpack-dev-serve
3. 修改webpack.config.js

  ```javascript
  entry: {
      index: ['webpack/hot/dev-server','./Src/View/Index/index']
  }
  
  ...
  
  loaders: [
  {
      test: /\.js$/,
      loaders: ['babel-loader'],
      include: __dirname + '/Src',
  ```
4. 修改.babelrc

      ```javascript
      {
        "presets": [
            "es2015",
            "react"
        ],
        "plugins": ["react-hot-loader/babel"] //主要加上这个插件
    }

      ```