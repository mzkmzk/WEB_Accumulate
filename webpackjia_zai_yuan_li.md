# webpack加载原理

# 思路

在最终的js头部放了一个自执行函数

```javascript
(function(modules){
  加载模块核心代码
})([
  function(module, exports,__webpack_require__) {
    模块0的代码
  },
  function(module, exports,__webpack_require__) {
    模块1的代码
  },
])
```

会把你写的所有模块都当成一个数组传递到modules中

然后看着webpack如何把你的代码一个个关联起来

化整为0,其实只要理解webpack怎么整合第0个模块和第1个模块,也就基本能理解webpack加载思路

```javascript
(function(modules) {
    //注册完毕的module (注册过不一定加载完成)
    var installedModules = {};
    function __webpack_require__(moduleId) {
         //检查改module是否已经注册过
        if(installedModules[moduleId])
            return installedModules[moduleId].exports;
        //若没注册过,就新建立一个
        var module = installedModules[moduleId] = {
 			exports: {},
 			id: moduleId,
 			loaded: false
 		};
 
       //绑定this,将module和其引用出去的对象暴露出来,__webpack_require__就是加载其他模块的方法
        modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

        //注册成功
 		module.loaded = true;
        //返回其暴露的对象
		return module.exports;
 	}
    ...
    return __webpack_require__(0);
})()

```


# 代码

```javascript
//开头代码

/******/ (function(modules) { // webpackBootstrap
/******/ 	// The module cache 已经注册(可能还在load当中)完毕的module
/******/ 	var installedModules = {};

/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {

/******/ 		// Check if module is in cache
/******/ 		if(installedModules[moduleId])
/******/ 			return installedModules[moduleId].exports;

/******/ 		// Create a new module (and put it into the cache)
/******/ 		var module = installedModules[moduleId] = {
/******/ 			exports: {},
/******/ 			id: moduleId,
/******/ 			loaded: false
/******/ 		};

/******/ 		// Execute the module function
/******/ 		modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);

/******/ 		// Flag the module as loaded
/******/ 		module.loaded = true;

/******/ 		// Return the exports of the module
/******/ 		return module.exports;
/******/ 	}
      
/******/ 	// 所有的modules包装在m中,expose the modules object (__webpack_modules__)
/******/ 	__webpack_require__.m = modules;

/******/ 	// 已经加载完毕的modules  expose the module cache
/******/ 	__webpack_require__.c = installedModules;

/******/ 	// __webpack_public_path__
/******/ 	__webpack_require__.p = "";

/******/ 	// Load entry module and return exports
/******/ 	return __webpack_require__(0);
/******/ })
/************************************************************************/
/******/ ([
/* 0 */
/***/ function(module, exports, __webpack_require__) {

```

# 