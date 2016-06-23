# 实现

> 2016-06-23

可以预览下初版<http://journey.404mzk.com/Index/> 

具体实现的技术有

1. React
2. webpack
3. flex

# 概括

目录如下

![目录](QQ20160623-0.png)

# 1. webpack

先说一下`package.json`,项目需要使用的依赖

主要分开发时依赖和运行时依赖

```javascript
  "dependencies": {
    "jquery": "3.0.0",
    "react": "15.1.0",
    "react-dom": "15.1.0",
  },
  "devDependencies": {
    "babel-core": "6.9.1", //babel核心库
    "babel-loader": "6.2.4", //babel核心库
    "babel-preset-es2015": "6.9.0", //babel转es6
    "babel-preset-react": "6.5.0", //babel转react
    "css-loader": "0.23.1", //webpack加载css
    "html-loader": "0.4.3", //wepack加载html,其实这里作用不大,因为html已经写在react里了
    "node-sass": "3.8.0", //webpack渲染sass
    "sass-loader": "3.2.1", //webpack渲染sass
    "style-loader": "0.13.1", //webpack渲染css
    "webpack": "1.13.1" //webpack
  }
```

# 参考

webpack入门: <https://cnodejs.org/topic/57528759adc77ac170409e79?hmsr=toutiao.io&utm_medium=toutiao.io&utm_source=toutiao.io>

flex参考:

<http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html>

<http://www.ruanyifeng.com/blog/2015/07/flex-examples.html>

<https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Flexible_Box_Layout/Using_CSS_flexible_boxes>