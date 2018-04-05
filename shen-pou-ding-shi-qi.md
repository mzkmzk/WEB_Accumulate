# 深剖定时器

# 小tips

### 回调函数中的参数

```javascript
var test_bibao = function(){
    
    for (var i = 0; i < 10; i++) {
        setTimeout(function(){
            document.querySelector('.j_test_params_1').innerHTML +=  i + ' '
        },0)
        
        setTimeout((function(a){
            document.querySelector('.j_test_params_1').innerHTML +=  a + ' '
        })(i) , 0)


        setTimeout(function(a){
            document.querySelector('.j_test_params_2').innerHTML +=  a + ' '
        },0, i)
    }
}
test_bibao()
```

这算是比较经典的闭包引起的问题了

第一个setTimeout中 因为for循环之后才执行的输出, 所以i一直都是10

虽然解决这个问题可以通过第一个settimeout这种方式

而最好的方式还是第三种, delay参数之后的参数 都会被放进回调的参数列表中

### 回调中的this

简单的说下 setTimeout中的this是全局环境 浏览器中就是window



# 参考链接

1. 阮一峰的基础讲解文章: http://javascript.ruanyifeng.com/advanced/timer.html#toc1
