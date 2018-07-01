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

一般来说 前端er最起码能知道作用域链取是 local作用域 -> 闭包作用域 -> window作用域 

但是有没有作用域是在local之前的呢? 有就是wich 和 try catch(e)中的catch(){}

体验demo: http://demo.404mzk.com/actionScope/index.html

