# Redux+React使用Ajax

## 1.在store中配置中间件

```javascript
define(function(require,exports) {
    const {createStore , applyMiddleware} = require('redux');
    const {order} = require('../reducers/index');
    const  {thunkMiddleware} = require('redux-thunk');
    
    exports.configureStore = function(initialState){
        const store = createStore(order,initialState,applyMiddleware(thunkMiddleware));

        return store;
    }
});
```

就是吧`redux-thunk`引入到store前

##2. action使用

```javascript
    function getUser(users){
        return {
            type: "LOAD_USERS",
            users: users
        }
    }

  exports.loadUsers = function(){
        (dispatch, getState) => {
            return $.getJSON(url,param,function(result){
                dispatch(getUser(result));
            });
        }
    }
```

redux不允许ajax不能直接引用,然后在回调函数中返回action的

必须像这样

你不能直接在ajax中return,而是需要在ajax中 dispatch别的action

但是这只是简单后,ajax的前后前中后我们都需要设置,看后文React+Redux+Ajax 应用进阶

