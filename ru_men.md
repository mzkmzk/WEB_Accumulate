# 入门

## 1. 安装环境

```javascript
sudo npm install webpack -g
```

## 2. 基本测试

代码地址<https://github.com/mzkmzk/Webpack_Example/tree/master/1-base>

1. entry.js

    ```javascript
    document.write("It works.");
    ```
2. index.html

    ```html
    <html>
    <head>
        <meta charset="utf-8">
    </head>
    <body>
        <script type="text/javascript" src="bundle.js" charset="utf-8"></script>
    </body>
</html>
    ```
3. 执行命令: `webpack ./entry.js bundle.js`  

基本很简单,`webpack`配置好了基本都OK 
    
## 3. 引入JS

代码地址<https://github.com/mzkmzk/Webpack_Example/tree/master/2-exports_js>

1. 复制`2. 基本测试`
2. 增加`content.js`

    ```javascript
    module.exports = "It works from content.js.";
    ```
3. 修改`entry.js`

     ```javascript
     - document.write("It works.");
      + document.write(require("./content.js"));
     ```
4. `$ webpack ./entry.js bundle.js

这部分使用了`exports`模块

## 4. 引入CSS

代码地址<https://github.com/mzkmzk/Webpack_Example/tree/master/3-require_css>

1. copy上一步的代码
2. `npm install css-loader style-loader`
3. 增加样式

    ```css
    body { 
      background: yellow ;
    }
    ```
4. 更新`entry.js`

      ```javascript
    + require("!style!css!./style.css");
    document.write(require("./content.js"));
      ```
5. `webpack ./entry.js bundle.js`

## 5. 命令引入CSS模块

代码路径<https://github.com/mzkmzk/Webpack_Example/tree/master/4-require_css_order>

1. copy上一步代码
2. 修改`entry.js`

    ```javascript
      - require("!style!css!./style.css");
      + require("./style.css");
      document.write(require("./content.js"));
    ```
3. 执行`webpack ./entry.js bundle.js --module-bind 'css=style!css'

## 6. 引入配置文件

代码地址<https://github.com/mzkmzk/Webpack_Example/tree/master/5-config_file>

1. copy上一步骤嗲吗
2. 新建webpack.config.js

  ```javascript
  module.exports = {
      entry: "./entry.js",
      output: {
          path: __dirname,
          filename: "bundle.js"
      },
      module: {
          loaders: [
              { test: /\.css$/, loader: "style!css" }
          ]
      }
  };
  ```
3. `webpack`

## 7. 开启本地Server

1. 安装Server: `sudo npm install webpack-dev-server -g`
    弹出警告
    ```
    npm WARN webpack-dev-server@1.14.1 requires a peer of webpack@>=1.3.0 <3 but none was installed.
npm WARN webpack-dev-middleware@1.6.1 requires a peer of webpack@1 || ^2.1.0-beta but none was installed.
    ```
2. 开启监听: `webpack-dev-server --progress --colors
`
3. 打开: `http://localhost:8080/`
## 基本命令

1. `webpack --progress --colors --watch`监听变化
2. 

  