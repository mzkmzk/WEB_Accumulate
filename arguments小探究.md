# arguments小探究

# 小代码实验

```javascript
    window.text = function(){
        var a =  arguments;
        var b =  $.makeArray(arguments);
        var c = Array.prototype.slice.call(arguments);
        $(b).each(function(index,elem){
            var d = $(this)
            console.log(d)
            var e = elem
            console.log(e)
            var f = this
            console.log(f)
        })
    }
    text(1,2,3,4,'55')
```

# 结果

![arguments](/assets/QQ20161224-1.png)