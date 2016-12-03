# import和require的区别

这里是在浏览器环境下而谈的

import和require都不能直接运行在浏览器

require是CommonJS的一部分

而import是ES2015的新规范

首先说说webpack通过babel转义这两个东西后的语法

# 转义中和这两有关系的

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