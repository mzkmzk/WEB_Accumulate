
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
    