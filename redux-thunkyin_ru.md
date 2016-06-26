# redux-thunk引入

## 1. 问题

因为我是通过自己项目配置的`requirejs`,所以无法引用npm安装`redux-thunk`

所以只能愚蠢的拷贝`redux-thunk`中lib的代码

但是会出现`redux.js middleware is not a function`的问题

## 2. 过程

先来看看`redux-thunk的代码`

```javascript
'use strict';

function thunkMiddleware(_ref) {
  var dispatch = _ref.dispatch;
  var getState = _ref.getState;

  return function (next) {
    return function (action) {
      return typeof action === 'function' ? action(dispatch, getState) : next(action);
    };
  };
}

module.exports = thunkMiddleware;
```

代码的具体作用可以参考<https://github.com/react-guide/redux-tutorial-cn/blob/master/09_middleware.js>

为了我们自己项目的`requirejs`就改成了

```javascript
'use strict';

define(function (require, exports) {
    function thunkMiddleware(_ref) {
        var dispatch = _ref.dispatch;
        var getState = _ref.getState;

        return function (next) {
            return function (action) {
                return typeof action === 'function' ? action(dispatch, getState) : next(action);
            };
        };
    }
    exports.thunkMiddleware = thunkMiddleware;
});
```

然后 浏览器就开始报错了.

`middleware is not a function`

看看报错的函数

```javascript
function applyMiddleware() {
    for (var _len = arguments.length, middlewares = Array(_len), _key = 0; _key < _len; _key++) {
        middlewares[_key] = arguments[_key];
    }

    return function (createStore) {
        return function (reducer, initialState, enhancer) {
            var store = createStore(reducer, initialState, enhancer);
            var _dispatch = store.dispatch;
            var chain = [];

            var middlewareAPI = {
                getState: store.getState,
                dispatch: function dispatch(action) {
                    return _dispatch(action);
                }
            };
            chain = middlewares.map(function (middleware) {
                return middleware(middlewareAPI);
            });
            _dispatch = _compose2["default"].apply(undefined, chain)(store.dispatch);

            return _extends({}, store, {
                dispatch: _dispatch
            });
        };
    };
}
```

出错的位置在

```javascript
chain = middlewares.map(function (middleware) {
    return middleware(middlewareAPI);
});
```

说的是找不到需要迭代的`函数`

`函数`是关键

再看看store中是如何配置的

```javascript
...
const  thunkMiddleware = require('redux-thunk');

    exports.configureStore = function(initialState){
        const store = createStore(order,initialState,applyMiddleware(thunkMiddleware));

        return store;
    }
```

原来`const  thunkMiddleware = require('redux-thunk');`

这样引入是整个js文件引入过来啊...这样给redux是一个js文件而非函数

## 3.解决方案

在sotre中引入时

` const  {thunkMiddleware} = require('redux-thunk');` 即可..