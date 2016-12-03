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



