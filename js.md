# JS

##1. 闭包变量

先看程序

```javascript
for(var index = 0 ; index<10;index++){
        var li = document.createElement("li");
        li.onclick = function(index){
            console.log(index);
        };
        ul.appendChild(li);
    }
```
初学者可能会认为生成后的li,点击时候,console.log出来的值是和index一样.

但是实际上console.log出来的都是10

为什么,因为在用户点击的li的时候,