# 数字加减

![数字加减](QQ20160331-0.png)
实现的功能

1. 按加减简单实现数字的加减
2. 点击`increment if odd `:当数字为奇数时才增加数字
3. `incrementAsync` 点击后1秒,数字才会增加. 

## 1. html
首先`html`上只是定义了一个节点,和引用JS

```html
<!DOCTYPE html>
<html>
  <head>
    <title>Redux counter example</title>
  </head>
  <body>
    <div id="root">
    </div>
    <script src="/static/bundle.js"></script>
  </body>
</html>
```

## 2.定义组件

这里主要负责View的显示,JS的触发哪个事件在这里只是占了个位,一些通用的组件方法可以放在这里,具体设置化的可以通过props传入

```javascript
import React, { Component, PropTypes } from 'react'

class Counter extends Component {
  constructor(props) {
    super(props)
    this.incrementAsync = this.incrementAsync.bind(this)
    this.incrementIfOdd = this.incrementIfOdd.bind(this)
  }

  incrementIfOdd() {
    if (this.props.value % 2 !== 0) {
      this.props.onIncrement()
    }
  }

  incrementAsync() {
    setTimeout(this.props.onIncrement, 1000)
  }

  render() {
    const { value, onIncrement, onDecrement } = this.props
    return (
      <p>
        Clicked: {value} times
        {' '}
        <button onClick={onIncrement}>
          +
        </button>
        {' '}
        <button onClick={onDecrement}>
          -
        </button>
        {' '}
        <button onClick={this.incrementIfOdd}>
          Increment if odd
        </button>
        {' '}
        <button onClick={this.incrementAsync}>
          Increment async
        </button>
      </p>
    )
  }
}

Counter.propTypes = {
  value: PropTypes.number.isRequired,
  onIncrement: PropTypes.func.isRequired,
  onDecrement: PropTypes.func.isRequired
}

export default Counter

```

## 3.调用组件

```javascript
import React from 'react'
import ReactDOM from 'react-dom'
import { createStore } from 'redux'
import Counter from './components/Counter'
import counter from './reducers'

const store = createStore(counter)
const rootEl = document.getElementById('root')

function render() {
  ReactDOM.render(
    <Counter
      value={store.getState()}
      onIncrement={() => store.dispatch({ type: 'INCREMENT' })}
      onDecrement={() => store.dispatch({ type: 'DECREMENT' })}
    />,
    rootEl  
  )
}

render()
store.subscribe(render)

```
在调用组件时

定义了其特殊的方法

注意点主要有以下几个

1. `const store = createStore(counter)`获取统一的store
2. `store.subscribe(render)`: 将render函数传递给store.subscribe

## 4.reducers 

```javascript
export default function counter(state = 0, action) {
  switch (action.type) {
    case 'INCREMENT':
      return state + 1
    case 'DECREMENT':
      return state - 1
    default:
      return state
  }
}
```

这里主要处理state的状态.

## 5.结合本例子说说Redux的架构图

![Refux架构图](QQ20160331-1.png)

1. Views层比较简单,就是我们在定义组件时`render`显示的views
2. Actuib Creators: 在调用组件时,定义了具体的点击时,会引发的action
3. Action: 注意定义的reducers,表示之前传过来要执行action,具体要改变哪一些store


