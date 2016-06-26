# 基础API

1. 遇到`<`开头,用HTML解析,遇到`{`用JS解析
2. `React库`用于生成一个组件
    
    1. `createClass`: 用于生成组件(组件名首字母必须大写 & 组件只能有一个顶级标签)
    2. `render`: 用于输出组件
    3. `propTypes`: 验证组件实例的属性是否符合要求,验证标准<http://facebook.github.io/react/docs/reusable-components.html>
    4. `getDefaultProps`: 设置组件属性的默认值
    5. `getInitialState`: 定义初始状态
    6. `state`: 读取属性`this.state`,设置属性`this.setState`,每次修改状态,`this.render`会再次执行
3. `this.props.属性` 获取调用本组件时候定义的属性
4. 定义`class`属性时,要写成`className`,定义`for`属性时,要写成`htmlFor`
5. `this.props.children`: 表示组件的所有子节点,可用`React.Children.map(this.props.children,function(child){...})`遍历所有子节点.
6. `this.refs` :用于获取实际DOM,在虚拟DOM定义某组件的ref属性,则可以在其他组件通过调用`this.refs.组件定义的ref`



## 参考资源

基础API <http://www.ruanyifeng.com/blog/2015/03/react.html>

