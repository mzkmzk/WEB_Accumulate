# react-router的生命周期 

# 背景 

在/search/QQ 跳转到/search/wechat遇到不触发重新搜索

最开始,我是希望这样来判断重新搜索

```javascript
componentWillReceiveProps(next_props){
    if ( next_props.router.params.key_name !== this.props.router.params.key_name) {
        this.search()
    }
}
```

但是`next_props.router.params.key_name`和`this.props.router.params.key_name`是一样的...

就是说嘛执行到componentWillReceiveProps的时候,路由已经切换完毕了

所以有必要去了解一下react-router的生命周期

# react-router跳转触发的react hook

单页面配置如下

```javascript
<Route path="/" component={App}>
  <IndexRoute component={Home}/>
  <Route path="invoices/:invoiceId" component={Invoice}/>
  <Route path="accounts/:accountId" component={Account}/>
</Route>
```

跳转主要有两种

1. 当前页面,切换到其他页面
2. 当前页面,改变参数切换到本页面

> 当前页面,切换到其他页面

```javascript
App	componentWillReceiveProps, componentDidUpdate
Home	N/A
Invoice	componentWillUnmount
Account	componentDidMount
```

这种切换会重新渲染其他页面的生命周期,并没有太多注意的地方

> 当前页面,改变参数切换到本页面

```javascript
App	componentWillReceiveProps, componentDidUpdate
Home	N/A
Invoice	componentWillReceiveProps, componentDidUpdate
Account	N/A
```

这里我们需要监听到页面参数的变化

`componentWillReceiveProps`获取到的next_props已经是最新的参数this_props和next_props已经表现为一致(虽然按道理componentWillReceiveProps应该是有改变,但是从router获取到的params已经是一致)

所以只能使用`componentDidUpdate`判断

```javascript
componentDidUpdate (prevProps) {
    // 上面步骤3，通过参数更新数据
    let oldId = prevProps.params.key_name
    let newId = this.props.params.key_name
    if (newId !== oldId)
      this.search()
  }
```



# 参考链接

1. https://react-guide.github.io/react-router-cn/docs/guides/advanced/ComponentLifecycle.html