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
      <div onScroll={(e,a) => _handleScroll(e,a)} 
    )
  }
  
 ```
 
 # DOM2
 
 在以前的写法,一般标签写个id,然后就绑定了
 
 但在react里最好使用ref
 
 ```javascript
 
 import ReactDOM , { render } from 'react-dom'
 
  _handleScroll(ev,a) {
        console.log('Scrolling!')
         console.log(a)
    }
    componentDidMount() {
        const list = ReactDOM.findDOMNode(this.refs.div_tasks)
        console.log(list)
        list.addEventListener('scroll', this._handleScroll)
    }
    componentWillUnmount() {
        const list = ReactDOM.findDOMNode(this.refs.div_tasks)
        list.removeEventListener('scroll', this._handleScroll)
    }
    
render() {
  return (
     <div ref="div_tasks"
  )
} 

 ```
 