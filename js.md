# JS

##1. 闭包变量

先看程序

```javascript
for(var index = 0 ; index<10;index++){
        var li = document.createElement("li");
        li.onclick = function(){
            console.log(index);
        };
        ul.appendChild(li);
    }
```
初学者可能会认为生成后的li,点击时候,console.log出来的值是和index一样.

但是实际上console.log出来的都是10

为什么,因为在用户点击的li的时候,这个for循环早已结束,index与for形成了一个闭包,因为onclick函数长期有效,导致for循环并没有被回收,但for循环已结束,所以index一直是10.

如何解决

```javascript
 for(var index = 0 ; index<header_children_length;index++){
        var li = document.createElement("li");
        li.innerText = header_children[index].innerText;
        li.onclick = (function(index){
            return function (){
                console.log(index);
            }
        })(index);
        ul.appendChild(li);
    }
```
这里没有直接把匿名函数赋给onclick,而是通过一个直接执行的函数把参数传给匿名函数.