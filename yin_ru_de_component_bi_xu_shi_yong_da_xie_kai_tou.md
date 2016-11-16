# 引入的component必须使用大写开头

# 情况

我有一个Component组件

```javascript
export default class Tasks extends Component {
```

然后在Contrainer中引入时

```javascript
import tasks from '../../Components/Tasks/Tasks'

...

render() {
    return (
       <section>
            <tasks />
```

然后发现我Tasks里定义的内容根本没出来

渲染出来的HTML是这样的

```html
<article id="root">
  <section data-reactroot="">
    <tasks></tasks>
  </section>
</article>
```

简直无语.......直接把我的tasks放出来了..内容没放出来

# 解决方案

在引入标签时,定义和使用变量要大写....就OK了