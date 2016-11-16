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
     
# 过程中遇到的问题

## 1 loaders中不要放react-hot

一开始参考链接1中是写

```javascript
    loaders: [{
      test: /\.js$/,

      // Use the property "loaders" instead of "loader" and 
      // add "react-hot" in front of your existing "jsx" loader
      // 使用 "loaders" 属性代替 "loader"
      // 然后在 "jsx" 加载器之前添加 "react-hot" 
      loaders: ['react-hot', 'babel']
```

而不是在babel中添加plugins的

但是这样会报错

```javascript
Module build failed: Error: React Hot Loader: The Webpack loader is now exported separately. If you use Babel, we recommend that you remove "react-hot-loader" from the "loaders" section of your Webpack configuration altogether, and instead add "react-hot-loader/babel" to the "plugins" section of your .babelrc file. If you prefer not to use Babel, replace "react-hot-loader" or "react-hot" with "react-hot-loader/webpack" in the "loaders" section of your Webpack configuration.
```

然后参考链接2

把loaders的react-hot改为

.babelrc的plugins加上`react-hot-loader/babel`即可

## 2. 不要按正常的webpack打包访问

之前没用热加载的时候,直接webpack -w 就可以打包了

但是这样是没有应用上热加载的

这样会报错

```javascript
index.bundle.js:116 Uncaught Error: [HMR] Hot Module Replacement is disabled.
```

解决方案:

用webpack-dev-server启动热加载服务

```javascript
webpack-dev-server --hot --inline
```
 
# 疑问

## 1. 无法帮助找到跟节点

```javascript
if (module.hot) {
  require('react-hot-loader/Injection').RootInstanceProvider.injectProvider({
    getRootInstances: function () {
      // Help React Hot Loader figure out the root component instances on the page:
      // 帮助 React Hot Loader 识别出页面中的根组件
      return [rootInstance];
    }
  });
}
```

这里是参考链接1的代码,是用来帮助找到根节点的

但是我执行这段代码的时候

```
Cannot resolve module 'react-hot-loader/Injection'
```

我来回换了基本react-hot-loader的版本还是有这个问题...

但是其实这段代码去掉 是没有太影响热加载的

所以暂时也没有解决..就直接用加这段代码,热加载还是能跑的通的

后面遇到与之有关的问题再来解吧

# 参考链接

1. https://fakefish.github.io/react-webpack-cookbook/Hot-loading-components.html
2. https://teamtreehouse.com/community/anyone-else-getting-an-error-when-including-react-hot-loader-in-the-webpack-config