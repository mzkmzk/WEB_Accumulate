# this知识看这就够

# this的4种情况

### 声明式和表达式 定义函数

```javascript

```

# 作用域链

作用域的作用是什么

当我们取变量时

```function
(function(){
    var a = 1
    function b(){
        var a = 2
        console.log(a)
    }
    b()
    
})()
```

它的输出会是2还是1呢? 

这取决于 console.log(a)中取的是 b函数的a 还是 最外层定义的a变量

一般来说 前端er最起码能知道作用域链取是 local作用域 -> 闭包作用域 -> global作用域 

但是有没有作用域是在local之前的呢? 有就是wich 和 try catch(e)中的catch(){}

体验demo: http://demo.404mzk.com/actionScope/index.html

普通的作用域

![普通的作用域](/assets/QQ20180701-112457.png)

with的作用域

![with作用域](/assets/QQ20180701-113003.png)

with作用域会排在local作用域前 在with内执行的获取变量

都会类似的先在 this.xxx(this为withObj) 的查找变量 然后再去查找local作用域

try catch(){}中catch的作用域

![try-catch中的作用域](/assets/QQ20180701-113227.png)

catch中会添加一个catch的作用域

当然with一般我们不用 catch里影响到作用域的就只有参数, 一般对我们影响不大

最主要的是 local -> 闭包 -> global的作用域查找

# 原型链

了解原型链之前先理解几个概念

1. prototype: xxx.protype指向的是xxx的原型(原型一般都是对象)
2. constructor: 存在于xxx.prototype.constructor, 指向xxx(xxx为构造函数)
3. __proto__: 每次被new的实例化对象 都会包含一个__proto__属性 其值是其constructor的prototype

从上可得出

1. 函数 都有 __proto__ 和 prototype, __proto__指向函数的构造函数的原型, prototype指向的是 函数的原型
2. 对象 只有 __proto__ 指向对象的构造函数的原型

下面列出几个对比 来理解原型链

体验地址: http://demo.404mzk.com/prototype_chain/

```javascript
var Foo = function(){}
var foo = new Foo()
foo.a = 1
Foo.prototype.a = 2
console.log(foo)

console.log(foo.prototype === undefined)
console.log(foo.__proto__ === Foo.prototype)
console.log(foo.__proto__.constructor === Foo)
console.log(Foo.prototype.constructor === Foo)

//对象
var obj = {}
console.log(obj)
console.log( obj.__proto__ === Object.prototype)
console.log(Object.prototype.__proto__ === null)

//函数
var fn = function(){}
console.log( fn )
console.log(fn.__proto__ === Function.prototype)
console.log(Function.prototype.__proto__ === Object.prototype)
console.log(Object.prototype.__proto__ === null)

//正则
var regExp = /a/
console.log( regExp )
console.log( regExp.__proto__ === RegExp.prototype)
console.log( RegExp.prototype.__proto__ === Object.prototype)
console.log( Object.prototype.__proto__ === null)

//数组

var array = []
console.log(array)
console.log(array.__proto__ === Array.prototype)
console.log(Array.prototype.__proto__ === Object.prototype)
console.log(Object.prototype.__proto__ === null)
```

可以看下几个类似的原型链

总结就是

1. 万物都是Object.prototype出来的

需要注意的额外点

Function.__proto__ === Function.prototype

可以说Function的构造函数还是其自身

好奇为什么Function的构造函数还是Function的读者

如果不是这样的话 那是先有Function的构造函数 还是先有Function呢? 

### 原型链的利用

最常见的就是用来做继承了

体验地址: http://demo.404mzk.com/prototype_chain/extend.html

```javascript
function inheritPrototype(subType, superType){
    var prototype = Object(superType.prototype)
    prototype.constructor = subType
    subType.prototype = prototype
}

var Grandfather = function(name){
    this.grandfahterName = name
}
Grandfather.prototype.grandfatherFn = function(){}

var Father = function(name){
    Grandfather.apply(this, arguments)
    this.fatherName = name
}
inheritPrototype(Father, Grandfather)
Father.prototype.fatherFn = function(){}

var Son = function(name){
    Father.apply(this, arguments)
    this.sonName = name
}
inheritPrototype(Son, Father)
Son.prototype.sonFn = function(){}

var son = new Son('mzk')
console.log(son)
console.log( typeof son) //object
console.log( son.__proto__ === Son.prototype)
console.log( Son.prototype === Father.prototype)
console.log( Father.prototype === Grandfather.prototype)
console.log( son instanceof Son)
console.log( son instanceof Father)
console.log( son instanceof Grandfather)
```

需要注意的是构造函数的.prototype 必须声明在 inheritPrototype函数之后 不然会被覆盖掉

考题: 为什么浏览器能判断son 是Son Father Grandfather的实例?

因为instanceof 主要是检验son.__protype__ === Son.prototype

# 参考链接

1. 原型链参考: https://segmentfault.com/a/1190000002900676
