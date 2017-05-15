# react阻止冒泡

# 背景

react中JSX中定义了

```javascript
<button onCLick = {this.a) class="j_button"}
>
```

```javascript
a(event){
     event.stopPropagation()
}

$(document).on('click','.j_button', () => {alert(1)})
```

结果alert(1)还是会出现

# 为何

react和jquery类似, 自己维护自己一套事件系统

