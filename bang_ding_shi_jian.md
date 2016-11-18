# 绑定事件

react绑定事件,其实和普通DOM差不多.

不过需要注意的是

如果是DOM2也就是addEventListener绑定的话 记得在compoentWillUnmount取消掉绑定,因为每个组件都可能渲染很多次的

# DOM0

也就是直接在标签上绑定

```javascript
 <div onScroll={e => _handleScroll(e)} 
 
 //或者如果不用参数的话
 
  <div onScroll={_handleScroll} 
  
  //如果需要闭包的函数,此时我需要a还有event都传给_handleScroll,怎么破
  render() {
    var a = 1
    return (
      <div onScroll={(e) => _handleScroll(e,a)} 
    )
  }
  
  
  
  
```