# scroll和resize的监听是否并发的

# 问题

就是我们在滚滚轮的时候,其实触发了N多scroll事件

那么是并发还是串行的呢?

执行完这个scroll监听事件,再触发另一个

还是不断的并行触发呢

# 实验

```javascript
//注意..最好另外开一个没什么网页的浏览器,因为写了死循环,会卡死的
var myScroll = function(){
    console.log('go myScroll')
    while(true) {
      console.log(111)
    }
}

window.onscroll = myScroll
```

假设1: 串行

1. go myScroll输出一次
2. 然后后面都是111
3. 直到浏览器卡死

假设2: 并行

1. go myScroll和111交替发出
2. 浏览器卡死

# 结果