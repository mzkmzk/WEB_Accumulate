# 深剖定时器

# 小tips

### 回调函数中的参数

```javascript
var test_bibao = function(){
    
    for (var i = 0; i < 10; i++) {
        setTimeout(function(){
            document.querySelector('.j_test_params_1').innerHTML +=  i + ' '
        },0)

        setTimeout(function(a){
            document.querySelector('.j_test_params_2').innerHTML +=  a + ' '
        },0, i)
    }
}
test_bibao()
```

这算是比较经典的闭包引起的问题了

第一个setTimeout中 因为for循环之后才执行的输出, 所以i一直都是10