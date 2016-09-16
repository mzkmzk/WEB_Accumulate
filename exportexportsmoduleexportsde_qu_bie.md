# export、exports、module.exports的区别

# exports和module.exports的区别

1. exports 是指向的 module.exports 的引用
2. module.exports 初始值为一个空对象 {}，所以 exports 初始值也是 {}
3. require() 返回的是 module.exports 而不是 exports
4. exports不能被重新覆盖,因为会断掉和module.exports的联系

## exports和export

export import主要是ES6的语法

看看babel是如何转换着两个的

```javascript
export default {
	Ajax,
	Brower,
	Cookie,
	Event,
}

转换成

exports.default = {
    Ajax: _Ajax2.default,
    Brower: _Brower2.default,
    Cookie: _Cookie2.default,
    Event: _Event2.default
};

export var firstName = 'K';

转换成

var firstName = exports.firstName = 'K';

```

# 参考链接

1. https://cnodejs.org/topic/52308842101e574521c16e06
2. https://cnodejs.org/topic/4faf88ee9f281d96650030aa