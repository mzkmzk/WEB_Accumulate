# 常用代码段收集

### 事件代理

```javascript
function _addEvent(obj, type, fn){
    return obj.addEventListener(type, fn, false)
}

function delegate(obj, className, type, fn){
    var dc =  ' ' + className + ''
    
    function cb(e){
        var target = e.target,
            c
        while(target !== obj){
            c = target.getAttribute('class') + ''
            if ( c.indexOf(className) !== -1){
                fn.call(target, e)
            }
            target = target.parentNode
        }
    }
    _addEvent(obj, type, cb)
}
```