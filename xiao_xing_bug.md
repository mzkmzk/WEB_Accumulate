# 小型bug

# Array的push返回是长度

```javascript
case 'ADD_DATA_ADD_TASK_DIALOG':
    
    return Object.assign({},state,{data: state.data.push(action.task)})
```

这里本来我想得是psuh后返回state.data本身..没想到是返回长度...折腾了15分钟......

//改成

```javascript
case 'ADD_DATA_ADD_TASK_DIALOG':
    state.data.push(action.task)
    return Object.assign({},state,{data: state.data})
```