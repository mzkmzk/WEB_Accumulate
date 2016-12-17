# 数据缓存Data

# 简介

jQuery的队列模块,动画模块,样式操作模块,事件系统模块,都需要用到缓存的Data

例如队列模块只负责如何去操控队列中的每一个元素如何执行,而队列的元素存储,是靠数据缓存Data的

# 结构

Data缓存模块是一个很典型的把方法存储在原型中,而动态的属性放在实体中

```javascript

function Data() {
	this.expando = jQuery.expando + Data.uid++;
}

Data.uid = 1;

Data.prototype = {
    cache: function(owner) // 获取owner的cache对象
    set: function(owner, data, value) {return 缓存值}
    get: function(owner, key) {return 缓存值}
    access: function(owner, key, value) {return 缓存值}
    remove: function(owner, key) {return 缓存值}
    hasData: function(owner) {return 缓存值}
}
```