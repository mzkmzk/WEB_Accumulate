# webpack的loader和loaders问题

# 原因

因为在webpack中之前是这样的

```javascript
loaders: [
    {
        test: /\.js$/,
        loader: ['react-hot','babel'],
        include: __dirname + '/Src',
    },
    
```

然后在打包时就报错....

```javascript
node_modules/webpack-core/lib/LoadersList.js:58
	if(element.loader) return element.loader.split("!");
```

因为我之前的loader只有babel,而后面加了个react-hot所以loader也要变成loader就可以了

# 参考链接

1. https://github.com/webpack/webpack/issues/1465