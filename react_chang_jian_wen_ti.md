# 常见错误

## 1. React循环生成相同元素时,建议使用key,例如循环生成li

这并不会引起编译错误,但会引起一个warning

```xml
Warning: Each child in an array or iterator should have a unique "key" prop.
```

我原先的代码是这样的

```javascript
this.props.fields.map(function(value){
    return (
        <li>
            ...
        </li>
    )
});
```

生成的li长这样

![](QQ20160328-0.png)

然后我往li里放了一个key

```javascript
this.props.fields.map(function(value,index){
    return (
        <li key="{index+3}">
            ...
        </li>
    )
});
```

然后生成的ul是这样的

![](QQ20160328-1.png)

我是根据JSON然后生成多个li元素,但是React建议我为每个li添加一个key元素

React为什么建议我们这样做,是为了让li更快的排序和摧毁.(笔者有点奇怪,react默认会生成reactid啊,为啥还要让我们自定义key)

假如往ul里放li,应该在ul里定义的li放key.

例如当li又是一个React组件,在li组件返回li中定义key是不好的,应该在ul中明显调用这个组件时就定义key



参考资料

<http://facebook.github.io/react/docs/multiple-components.html#dynamic-children>

## 2. reduce传统方式改变state

问题: 在reduce中,我要改变state的一个对象users,里面存放着数组

代码: 

```javascript
 switch (action.type) {
            case 'SELECT_USER' :
                state.users = state.users.map(user =>
                    user.id === action.id ?
                        Object.assign({},user,{selected: ! user.selected}) :
                        user);
                console.log(state);
                return  state;
            default:
                return state;
        }
```

疑问: 

在`console`显示出的state是正常的,但是store中的state并没有发生改变

修正:

```javascript
switch (action.type) {
    case 'SELECT_USER' :
        const users = state.users.map(user =>
            user.id === action.id ?
                Object.assign({},user,{selected: ! user.selected}) :
                user);
        return  Object.assign({},state,{users: users})

    default:
        return state;
}
```

这样的话,store中的state也会改变,但是为什么呢?难道他们背后有什么肮脏的交易吗

后续分析:`Object.assign`究竟做了什么鬼