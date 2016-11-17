# ReduxDevtool

# 效果

![reduxdevtool](QQ20161117-0.png)

其实配合上热加载,能比较方便的看到redux的store的状态变化,还是不错的

# 实现方案

1. 可以在component里放一个devtools组件,显示在页面的右边
2. 安装chrome插件

# how

这里说的是安装chrome插件的情况下如何使用

1. 安装插件: https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd
2. 可以先过一下插件文档和demo: https://github.com/zalmoxisus/redux-devtools-extension
3. 改变store状态 

  1. 从redux中获取compose
  2. 设置redux插入变量`const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose
`
  3. 然后把之前的异步放进去state

    ```javascript
      exports.configure_index_store = function(initial_state) {
          return createStore(index,initial_state,composeEnhancers((
          applyMiddleware(thunk)
        )))
  }

    ```
4.     
    我原本设置store的文件
    
      ```javascript
   import { createStore , applyMiddleware, compose } from 'redux'
   import thunk from 'redux-thunk'
   import { index } from '../Reducers/index'

    exports.configure_index_store = function(initial_state) {
        return createStore(index,initial_state,applyMiddleware(thunk)
      )))
    }

    ```
    
    改成支持插件获取store后的设置
    
    ```javascript
   import { createStore , applyMiddleware, compose } from 'redux'
   import thunk from 'redux-thunk'
   import { index } from '../Reducers/index'

   const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose

    exports.configure_index_store = function(initial_state) {
        return createStore(index,initial_state,composeEnhancers((
        applyMiddleware(thunk)
      )))
    }

    ```
