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
 
 # 点击事件传参
 
 问题是这样的..我在map一个数组生成列表,然后点击某行需要获取当行的数据
 
 ```javascript
<button onClick={this.handleClick.bind(obj, props0, props1, ...}></button>

handleClick(porps0, props1, ..., event) {
    // your code here
}
 ```
 
 注意这里的obj就是后面props0所在的对象
 
 而被绑定的事件handleClick最后一个参数是event,obj是不会放在参数里的
 
 但是我现在就是想整个obj都传过去
 
 ```javascript
 
  tasks.data.map((data_one) =>
   <div key={data_one.task_id} >
   
      <div className="file_opt">
          <a  onClick={ this.deleteTask.bind({},data_one) } href="javascript:;" title="删除" className="ico_file ico_f_del"></a>

      </div>
 ```
 
 以上的代码中我可以在第一个参数里获取到data_one,但是这不科学,因为我的第一个参数是{},照理第二个参数要是第一个参数的属性,但是我这样就ok了..奇怪....
 
 而且虽然说的是最后一个参数是event其实是个react定义PROXY,而不是元素的Event,原生的Event,可以在真正proxy的后面第二个参数,真正的Event就出来了......
 
 ```javascript
     deleteTask(a,b,c,d,e,f){
     
         //a: data_one
         //b: PROXY
         //c undeifned
         //d Event
         //e undefined
         //f undefined
    }
    
    <a  onClick={ this.deleteTask.bind({},data_one) }
 ```
 
 
 
 # 参考链接
 
 1. react点击穿参: http://www.jianshu.com/p/d745514e547b
 