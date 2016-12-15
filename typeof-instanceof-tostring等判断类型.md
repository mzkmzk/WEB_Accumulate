# typeof instanceof toString 等判断类型

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
console.log(typeof document.getElementById) // 高级浏览器 function ,IE8及其以下 object

```

# instanceof 

instanceof不仅检验构造这个对象的构造器,还检验原型链上的构造器

所以 所有非基本类型 & undefined & null, instanceof Object都是true

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



