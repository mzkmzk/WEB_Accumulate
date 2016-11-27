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

# + 和 ?:顺序问题


```javascript
'chk_file '+ true ? 'checked':''
"checked"
```

一开始本来想 先后面判断, 然后再?号.结果是先+再? ....