# 统计table选择列总数

## 1.Redux+React的调用机制

先来看看Redux的架构图

![Redux的架构图](TB1SsWQLFXXXXXMXVXXXXXXXXXX-1170-514.jpg_600x600.jpg)

Views通过用户的交互而触发了`Action Creators`

`Action Creators`的作用就是return一个`Action`

`Action`一般为一个数据对象,该数据对象的作用是给reducer作为参考,如何改变Store

## 2. 功能分析

先看一下重构师给出的页面效果

![示例图](QQ20160408-1.png)


功能:
tou't
1. 左上角有统计总人数
2. 人数列可以勾选上
3. 左下角有统计勾选的人数

## 3. 分解View模块

![View分解](QQ20160408-5.png)

把View分解成这样的组件,然后通过AcitivityOrder这个容器去调用他们.

至于容器和组件的区别有

|            | 容器组件              | 展示组件              |
|------------|-----------------------|-----------------------|
| 位置       | 最顶层，路由处理      | 中间和子组件          |
| 使用 Redux | 是                    | 否                    |
| 读取数据   | 从 Redux 获取 state   | 从 props 获取数据     |
| 修改数据   | 向 Redux 发起 actions | 从 props 调用回调函数 |

简单点来说,`action creator`和全局的`state`都是在容器中引用的

然后容器可以根据组件所要满足的功能,然后一层层的把部分`action creator`和`state`往组件传递

## 4. 思考store对象内容

这个例子比较简单

顶级的store只需要保存一个`orderUsers`数组即可

![store](QQ20160408-6.png)

## 5. 制作reduces

reduces只是用来更改store

这里更改store只有当用户点击勾选时会发生改变.

```javascript
exports.orderUsers = function(state = initialState , action) {
        switch (action.type) {
            case 'SELECT_USER' :
                return state.map(user =>
                    user.id === action.id ?
                        Object.assign({},user,{selected: ! user.selected}) :
                        user
                )

            default:
                return state;
        }

    }
```

当用户勾选时,只需要知道这是哪一列被勾选了即可改变store.

reduce里都会返回一个完整的全局state,我们只需要筛选出被勾选的那列,然后对其`selected`属性取反即可

## 6. 制作action

OK,我们知道当用户点击勾选之后,reduce要根据id来更改store.

那么我们只需要在action中告诉它

1. 发生的勾选事件
2. 被勾选的ID是

所以在action中

```javascript
exports.selecOrdertUser = function(id){
        return {
            type: "SELECT_USER",
            id: id
        }
    }
```

## 7.定义顶级容器

好了,我们知道当用户勾选了一列之后,action和reduce要怎么做了.

但是如何在View中定义何时发送action creator的请求呢?

之前在第三点说过,任何的action和store都是从顶级逐渐往下传递的

所以我们先定义顶层的`ActivityOrder`

```javascript
 class ActivityOrder extends Component {
        render() {
            const {orderUsers,actions} = this.props
            return (
                <section className="entry_list">
                    <OrderTab />
                    <OrderUserCount orderUsersLength={orderUsers.length}/>
                    <OrderTable selectOrderUser={actions.selecOrdertUser} orderUsers={orderUsers}/>
                    <OrderButtom orderUsers={orderUsers}/>
                </section>
            )
        }
    }

    function mapStateToProps(state) {
        return {
            orderUsers: state.orderUsers
        }
    }

    function mapDispatchToProps(dispatch) {
        return {
            actions: bindActionCreators(UserAction,dispatch)
        }
    }

    exports.ActivityOrder = connect(
        mapStateToProps,
        mapDispatchToProps
    )(ActivityOrder);
```

估计会懵逼的点

1. `mapStateToProps`: 当state发生变化后,触发该方法,返回需要给组件使用的state.
2. `mapDispatchToProps`: 返回的值就是`actions`,容器就可以获取到所有的actions中定义的`action creators`
3. `connect(mapStateToProps,mapDispatchToProps)(ActivityOrder)`: 这里可以简单的理解为,把Redux定义的这两个方法和容器绑定起来
4. `const {orderUsers,actions} = this.props`: 容器中根据`this.props`获取的就是`mapStateToProps`和`mapDispatchToProps`返回的对象.
5. 容器就根据全局状态和action,分配给各主键向下传递

## 8. 各组件获取上级传递的部分state和action

这里就简单的以`OrderTableBody`这个组件举例

首先搞清楚传递链

`ActivityOrder->OrderTable->OrderTableBody`

`ActivityOrder`需要把`orderUsers={orderUsers}`传递给`OrderTable`

`OrderTable`再把`orderUsers={orderUsers}`传递给`OrderTableBody`

当`OrderTableBody`获取到`orderUsers`以后,如何生成相对应li

```javascript
class OrderTableBody extends Component {
        render() {
            const { orderUsers, selectOrderUser } = this.props

            return (
                <div className="tbody">
                    <ul>
                        {
                            orderUsers.map(orderUser =>
                                    <li key={orderUser.id}>
                                        <a href="entry_detail.html">
                                            <span className="a"><div className="img"><img src={orderUser.title} /></div></span>
                                            <span className="b">{orderUser.name}</span>
                                            <span className="c">{orderUser.ticket}</span>
                                            <span className="d">{orderUser.price_num}</span>
                                        </a>
                                        <span className="e"><div className={classnames({ico: true ,selected: orderUser.selected})} onClick={() => selectOrderUser(orderUser.id)}></div></span>
                                    </li>
                            )
                        }

                    </ul>
                </div>
            )
        }
    }

    exports.OrderTableBody = OrderTableBody;
```

可能懵逼的点

1. `const { orderUsers, selectOrderUser } = this.props`: orderUsers, selectOrderUser都是从容器中一级级传递下来的
2. `className={classnames({ico: true ,selected: orderUser.selected})}`: 例如`selected: orderUser.selected`,当`orderUser.selected`为true时,selected这个class就显示
3. `selectOrderUser`: 这里的`selectOrderUser`就是在action creator中定义的方法


## 9. 总结开发步骤

1. 划分组件,把重构师的HTML复制过去各组件
2. 思考store形式,定义好reduce
3. 根据reduce写出action
4. 在容器顶端分发state和action到各组件去.