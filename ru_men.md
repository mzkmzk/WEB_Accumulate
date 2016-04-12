
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

1. copy上一步代码