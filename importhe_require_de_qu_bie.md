# import和require的区别

这里是在浏览器环境下而谈的

import和require都不能直接运行在浏览器

require是CommonJS的一部分

而import是ES2015的新规范

首先说说webpack通过babel转义这两个东西后的语法


# babel转义区别

```javascript
	Object.defineProperty(exports, "__esModule", {
	    value: true
	});
	exports.test3 = undefined;

	var _test = __webpack_require__(6);

	var _test2 = _interopRequireDefault(_test);

	function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

```

这代码应该在webpack编译

一般你在代码中有直接export而非modules.export的话

就会有

```javascript
	Object.defineProperty(exports, "__esModule", {
	    value: true
	});
```

而如果使用import 而非 require的话就会有了

```javascript
	var _test = __webpack_require__(6);

	var _test2 = _interopRequireDefault(_test);

	function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }
    
//如果使用require的话就只有

	var _test = __webpack_require__(6);

```

这两个其实使用差不多


# 循环加载相关

虽然http://es6.ruanyifeng.com/#docs/module中说加载方式和import 和require不一样 前者为索引,后者为拷贝

但笔者觉得这个export货module.exports有关系而已,和这个是没关系的

当然,我和阮一峰老师说的环境不一样

他是站着ES6的角度上看的

而我是站着编译可运行在浏览器的ES6说的
