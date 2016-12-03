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
以下是module.exports
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
    //如果是module.exports
	module.exports = b;
   //如果是export default
   exports.default = b;
   
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
     //如果是module.exports
	module.exports = c;
   //如果是export default
    exports.default = b;
```

如果是module.exports的方式

在c引入b时候

```
var _b = __webpack_require__(8);
```
就会把b当时的exports空对象给引过来

而后面当c加载完,到b加载的时候

b的module.exports = b是`不会去关联到c文件去的`

因为整个module.exports都被换掉了!!!(重点)

b中如果是export default 会转化成 export.default = b

此时c中引用的是b的module.exports,而此时是多了个属性,索引关联还是在的,没有去掉,所以就有了c文件自动再用到
b时,就能找到b刚export default的东西

# 参考链接

1. https://cnodejs.org/topic/52308842101e574521c16e06
2. http://es6.ruanyifeng.com/#docs/module
3. https://cnodejs.org/topic/4f16442ccae1f4aa27001045
4. https://cnodejs.org/topic/5231a630101e574521e45ef8


