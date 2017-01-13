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

# foreach

```javascript
Global_Data.bullets.forEach(function(element, index){
    if (element.lead_guid === leader.guid) {
        remove_bullets_total++;
        Global_Data.bullets.splice(index,1);
    }
})
```

其实这样是一个循环,因为是顺序执行,所以这里的foreach其实跟数组长度,然后和i++循环没有太大差别,因为循环中删除了一些元素,所以影响其原本的长度,会导致其忽略最后一些元素没进入循环

解决方案是用倒序或者复制数组