# 知识点

## 1. React生命周期

1. Mounting: 已插入真实DOM
2. Updating: 正在被重新渲染
3. Unmounting: 已移出真实DOM

周期前后触发的函数

1. componentWillMount()
2. componentDidMount()
3. componentWillUpdate(object nextProps,object nextState)
4. componentDidUpdate(object preProps,object preState)
5. componentWillUnmount()

React特殊状态的处理函数

1. componentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
2. shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用

![react声明周期](3-3-component-lifecycle.jpg)


## 2. 坑点

1. 每次render的return中,只能有一个顶级标签.

