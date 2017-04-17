# 热更新

这里的热更新为传统的HTML CSS JS热更新,不包含react

利用的是webpack-hot-middleware

https://github.com/glenjamin/webpack-hot-middleware

# 步骤

> 安装

npm install --save-dev webpack-hot-middleware

npm install --save-dev express
> 配置webpack.config

```javascript
plugins: [
    // OccurenceOrderPlugin is needed for webpack 1.x only 
    new webpack.optimize.OccurenceOrderPlugin(),
    new webpack.HotModuleReplacementPlugin(),
    new webpack.NoErrorsPlugin()
]
```

>  增加webpack-hot-middleware/client到入口

```javascript
entry: ['webpack-hot-middleware/client', 'index.js]
```

> 配置node服务器

新建serve.js

````javascript
var webpack = require('webpack');
var webpackConfig = require('./webpack.config');
var compiler = webpack(webpackConfig);
var http = require('http');

var express = require('express');
 var app = express();
app.use(require("webpack-dev-middleware")(compiler, {
    noInfo: true, publicPath: webpackConfig.output.publicPath
}));

app.use(require("webpack-hot-middleware")(compiler, {
    log: console.log, path: '/__webpack_hmr', heartbeat: 10 * 1000
  }));

app.get("/", function(req, res) {
  res.sendFile(__dirname + '/index.html');
});
if (require.main === module) {
  var server = http.createServer(app);
  server.listen(process.env.PORT || 1616, function() {
    console.log("Listening on %j", server.address());
  });
}
```