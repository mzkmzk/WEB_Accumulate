# module.exports和export的区别

有些写法是

module.exports = ...

或者 export default ...

这两者有什么区别

# 转义

在webpack+babel编译后,两种写法分别会变为

```javascript
//转变前后无变化
module.exports = ...

//而export default会转变成
exports.default = ...
//同效于
module.exports.default = ...
```

# 不同

这样其实大多数情况也差不多

但是循环依赖就不一样了
```
假如 index -> b -> c
             b <- c
```

b文件
```javascript
	var _c = __webpack_require__(9);

	var _c2 = _interopRequireDefault(_c);

	function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

	var b = {
	    a: function a() {
	        console.log('调用1');
	        _c2.default.a();
	    }
	};

	module.exports = b;
```

c文件
```javascript
/***/ function(module, exports, __webpack_require__) {

	'use strict';

	var _b = __webpack_require__(8);

	var _b2 = _interopRequireDefault(_b);

	function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

	var c = {
	    a: function a() {
	        console.log('调用');
	        _b2.default.a();
	    }
	};

	module.exports = c;
```

在c文件里的c.a()是永远找不到的,因为c只是一个空的object

为什么是空object?

# webpack加载机制

```javascript
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
```

先加载b模块,然后b的exports还是空时候,就要去加载c文件

c文件里放着的是b,exports 为空

