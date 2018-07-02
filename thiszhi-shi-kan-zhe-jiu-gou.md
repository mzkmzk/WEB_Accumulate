# this知识看这就够

# 声明提升



# this的取值

### 直接定义function

```javascript
function testFn(){
    console.log(this)
}
testFn()
```
这样调用 this在非严格模式下为 window 在严格模式下为 undefined

### 属性方法调用

```javascript
var testObj = {
    data: 'testObjData',
    testFn: function(){
        console.log(this.data)
    }
}
testObj.testFn() //testObjData
```

方法函数调用时 this为调用对象

### 构造函数

var testFn = function(){
    this.data = 'counstructoredData'
}

new testFn()

在new的时候 构造函数this是被实例化的对象

以上都是常规的this调用

那么js 有没有一些改变常规this的方法呢?

### 改变this的方法 bind apply call 箭头函数

绑定this的 优先级是 

箭头函数 > bind > apply、call > 正常this

tips: 

1. 一旦函数被bind 或者 箭头函数定义时, 假如被new 函数 会报错`xxx is not a constructor`
2. 如果一个函数连续被bind两次, 则this取第一次bind

可以看下demo http://demo.404mzk.com/this/

```javascript
     
var data = 'globalData'
//bind apply 箭头会遵循哪个?
var bindThis = { data: 'bindThisData'},
    bind2This = { data: 'bind2This'},
    applyThis = { data: 'applyThisData'},
    callThis = { data: 'callThisData'},
    testObj = {
        data: 'testObjData',
        simple: function(){
            var testFn = function(){
                console.log(this.data)
            },
            obj = { data: 'objData'}
            console.log(this.data)
            testFn()
            obj.testFn = testFn
            obj.testFn()
        },
        all: function(){
            var testFn = () => {
                console.log(this.data)
            }

            testFn = testFn.bind(bindThis)
            testFn.apply(applyThis)
            testFn()
        },
        binAndApply: function(){
            var testFn =function(){
                console.log(this.data)
            }

            testFn = testFn.bind(bindThis)
            testFn.apply(applyThis)
            testFn()
        },
        
        bind2: function(){
            var testFn = function(){
                console.log(this.data)
            }
            testFn1 = testFn.bind(bindThis)
            testFn2 = testFn1.bind(bind2This)
            testFn2()
        },
        constructorAndArrow: function(){
            var testFn = () => {
                console.log(this.data)
            }
           
            new testFn()
           //会报错
        },
        constructorAndBind: function(){
            var testFn = () => {
                console.log(this.data)
            }
            testFn = testFn.bind(bindThis)
           
            new testFn()
        }

    }
    console.log('testObj.simple 开始')
    testObj.simple()
    console.log('testObj.all 开始')
    testObj.all()
    console.log('testObj.binAndApply 开始')
    testObj.binAndApply()
    console.log('testObj.bind2 开始')
    testObj.bind2()
    
    try{
        console.log('testObj.constructorAndArrow 开始')
        testObj.constructorAndArrow()
    }catch(e){
        console.log(e)
    }
    try{
        console.log('testObj.constructorAndBind 开始')
        testObj.constructorAndBind()
    }catch(e){
        console.log(e)
    }
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

### 何时创建Scope

除了Global作用域 其他作用域 都是 function创建起来的作用域

现在ES6多了一种

```javascript
{
    let x = 0;
}
console.log(x); // Uncaught ReferenceError: x is not defined
```

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

最好看下网上的万能原型链图

![原型链图](/assets/68747470733a2f2f7261772e6769746875622e636f6d2f6d61787a68616e672f6d61787a68616e672e6769746875622e636f6d2f6d61737465722f61727469636c65732f696d616765732f6f6f702d342e6a7067.jpeg)

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
