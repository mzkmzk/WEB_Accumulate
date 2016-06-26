# API

1. `bindActionCreators`: 能自动将多个action创建函数绑定到dispatch()方法中
2. `createStore(reducer, [initialState])`: 

    1. 参数: 
      1. renducer(function): 接受两个参数,分别是当前的state树和要处理的action,返回新的state树
      2. initialState: 初始时的state
    2. 返回值: 新的state树
3. `Store`: 支持方法: 
  
    1. getState: 返回当前的state树,与最后一个reducer返回值相同.
    2. dispath(action): 分发action,触发state变化的唯一路径.
    3. subscribe(listener): 每当dispath action 的时候会执行回调.返回值是一个可以解绑变化监听的函数
    4. replaceReducer(nextReducer): 替换store当前用来计算state的reducer
4. `combineReducers(reducers)`: 将多个reducer合并为一个

      1. 参数: reducers(object) 一个对象
      2. 返回值: (Function): 一个调用reucers对象里所有reducer的reducer,并且构造一个与reducers对象结构相同的state对象
5. `applyMiddleware(...middlewares)`: 

    1. 参数:
        1. `...middlewares(arguments)`: 遵循Redux middleware API函数.每个middlewares接受Store的dispath和getState函数作为命名参数,并返回一个函数.该函数会被传入被称为next的下一个middlewares的dispath方法,并返回一个接受action的新函数,这个函数可以直接调用next(action),或者在其他需要的时刻调用,甚至根本不用调用它,调用链中最后一个middlewares会接受真实的store的dispath方法作为next参数,并借此结束调用链,所以middlewares的函数签名是({getState,dispath}) => next => action
        2. 返回指: (Function): 一个应用了middlewares后的store enhancer.这个store enhancer就是一个函数,并且需要应用到createStore.它返回一个应用了middleware的新的createStore.
        
6. `bindActionCreators(actionCreators, dispatch)`: 惟一使用 bindActionCreators 的场景是当你需要把 action creator 往下传到一个组件上，却不想让这个组件觉察到 Redux 的存在，而且不希望把 Redux store 或 dispatch 传给它。

    1. 参数

          1. actionCreators (Function or Object): 一个action creator,或者键值是action creator的对象
          2. dispath(Function): 一个dispath函数,由Store提供
    2. 返回值: 一个原对象类似的对象,只不过这个对象中的每个函数都可以直接dispath action,如果传入是一个函数,返回也是一个函数.
7. `compose(...functions)`:从右到左组合多个函数

