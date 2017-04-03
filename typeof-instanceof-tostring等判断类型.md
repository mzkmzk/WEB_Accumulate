# typeof instanceof toString 等判断类型

<!-- toc -->

# typeof 

可靠的比较-检测基本类型和function

```javascript
console.log(typeof 'k') //string
console.log(typeof 123) // number
console.log(typeof true) //boolean
console.log(typeof undefined) //undefined
console.log(typeof function a(){} ) //function
```

不可靠的比较-检测null和索引值

```javascript
console.log(typeof null) //object
console.log(typeof []) //object
console.log(typeof {}) //object
console.log(typeof new Date()) //object
console.log(typeof new RegExp()) //object
// 高级浏览器 function ,IE8及其以下 object
//所以判断document里的一些方法是否存在的话 可以if('getElementById' in document)
console.log(typeof document.getElementById) 

```

# instanceof 

instanceof不仅检验构造这个对象的构造器,还检验原型链上的构造器

所以 所有非基本类型 & undefined & null, instanceof Object都是true


# toString

如何判断数组

```javascript
console.log(typeof [] ) //object
[].toString() // ''
Object.prototype.toString.call([]) //"[object Array]"

而instanceof Array在跨iframe是不好使的
```
而在ES5中,引入了Array.isArray()

toString啥时候用: 判断浏览器内置对象时

# 为何判断是否为函数不用instanceof

```javascript
function a(){}
console.log(a instanceof Function ) //true
console.log(typeof function a(){} ) //function
```

这样看,两者都能判断变量是否都为function

但为何不建议用`instanceof Function`

因为在跨frame里判断的话每个frame都有自己的Function函数

假如在frame A中定义了function a

而在frame B中执行 a instanceof Function 是返回false的

因为B的Function 不等于A的Function

# 总结

1. typeof: 判断基本类型和function时用
2. instanceof: 判断自定义对象时用
3. toString: 判断浏览器内置对象时用

一般判断type的方法

` (obj === null || obj === undefined) ? String(obj) : Object.prototype.toString.call(obj).match(/\[object (\w+)\]/)[1].toLowerCase()`

返回 null ,undefined, number, array, object 等

至于为啥`(obj === null || obj === undefined) ? String(obj) `

其实不写这个也可以,后面一样可以返回正确的值,这样写的原因是效率相对高点 


