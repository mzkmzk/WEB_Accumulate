# webpack打包插件

# 问题

如果使用webpack,普通的打包是无法向外面export模块的

# 解决

## 使用babel编译

其实可以分两种 

一种是直接引入打包后的JS,这样需要在webpack设置

```javascript
var libraryName = 'k_logging';

module.exports = {
    entry: {
        index: './Src/index',
    },
    //devtool: "cheap-module-source-map",
    output: {
        path: 'Public',
        library: libraryName,
        libraryTarget: 'umd',
        umdNamedDefine: true,
```

引入了

```
library: libraryName,
libraryTarget: 'umd',
umdNamedDefine: true,
```
之后,生成的JS就会在打包后的JS顶部

```javascript
(function webpackUniversalModuleDefinition(root, factory) {
	if(typeof exports === 'object' && typeof module === 'object')
		module.exports = factory();
	else if(typeof define === 'function' && define.amd)
		define("k_logging", [], factory);
	else if(typeof exports === 'object')
		exports["k_logging"] = factory();
	else
		root["k_logging"] = factory();
})(this, function() {
return /******/ (function(modules) { // webpackBootstrap
```

第二种是只babel模块,不打包,由引入方打包,这种是最好的

一般直接babel Src --out-dir Lib即可(先`sudo npm install --global babel-cli`)